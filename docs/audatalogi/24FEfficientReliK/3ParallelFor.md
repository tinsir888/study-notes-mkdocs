---
title: Efficient ReliK 3 Parallel For Loop
author: tinsir888
date: 2024/10/1
cover: /img/AUrelik.png
katex: true
tags:
  - 笔记
  - 数据挖掘
  - 人工智能
  - 知识图谱
categories:
  - AUDatalogi
  - 数据挖掘
  - 知识图谱嵌入可靠性衡量
abbrlink: 24929ff8
---

This week, I am mainly working on parallelization of enumerating $x_{h'r't'}$: `for u,v in nx.DiGraph(M).subgraph(subgraph).edges()`.

# Target Code

```python
for subgraph in subgraphs:
    count = 0
    sib_sum = 0
    sib_sum_h = 0
    sib_sum_t = 0
    start_uv = timeit.default_timer()
    for u,v in nx.DiGraph(M).subgraph(subgraph).edges():
        w, w1, w2 = heur(u, v, M, models, entity_to_id_map, relation_to_id_map, all_triples_set, full_graph, sample, datasetname)
        count += 1
        sib_sum += w
        sib_sum_h += w1
        sib_sum_t += w2
        end_uv = timeit.default_timer()
        print(f'have done subgraph: {id(subgraph)} in {end_uv - start_uv}')

        sib_sum = sib_sum/count
        sib_sum_h = sib_sum_h/count
        sib_sum_t = sib_sum_t/count
        model_ReliK_score.append(sib_sum)
        model_ReliK_score_h.append(sib_sum_h)
        model_ReliK_score_t.append(sib_sum_t)
        tracker += 1
        if tracker % 10 == 0: print(f'have done {tracker} of {len(subgraphs)} in {embedding}')
```

# :sob:First Try: `ThreadPoolExecutor`

```python
for subgraph in subgraphs:
    count = 0
    sib_sum = 0
    sib_sum_h = 0
    sib_sum_t = 0

    edges = list(nx.DiGraph(M).subgraph(subgraph).edges())

    start_uv = timeit.default_timer()
    results = None
    with ThreadPoolExecutor() as executor:
        results = list(executor.map(lambda x: heur(x[0], x[1], M, models, entity_to_id_map, relation_to_id_map, all_triples_set, full_graph, sample, datasetname), edges))
	for w, w1, w2 in results:
        count += 1
        sib_sum += w
        sib_sum_h += w1
        sib_sum_t += w2
    end_uv = timeit.default_timer()
    print(f'have done subgraph: {id(subgraph)} in {end_uv - start_uv}')
    sib_sum = sib_sum/count
    sib_sum_h = sib_sum_h/count
    sib_sum_t = sib_sum_t/count
    model_ReliK_score.append(sib_sum)
    model_ReliK_score_h.append(sib_sum_h)
    model_ReliK_score_t.append(sib_sum_t)
    tracker += 1
    if tracker % 10 == 0: print(f'have done {tracker} of {len(subgraphs)} in {embedding}')
```

On `Countries`, for each subgraph it takes around 3000 ms, while on `SmallcodeX`, it takes around 120 s per subgraph.

## Result:weary:

This approach is definitely negative optimization.

:x:ABORTED.

# :sob:Second Try: Parallelization on GPU

Then try to do parallelization using PyTorch + CUDA.

```python
for subgraph in subgraphs:
    count = 0
    sib_sum = 0
    sib_sum_h = 0
    sib_sum_t = 0
    start_uv = timeit.default_timer()

    edges = list(nx.DiGraph(M).subgraph(subgraph).edges())

    len_edges = len(edges)

    #print(len(edges))
    num_parallel = 100

    stream = [torch.cuda.Stream() for _ in range(num_parallel)]
    #stream = torch.cuda.Stream()

    results = [None] * num_parallel

    block_size = (len(edges)) // num_parallel

    # Parallelize the heur computation using PyTorch
    #with profiler.profile(use_cuda=True, record_shapes=True) as prof:
    for i in range(num_parallel):
        with torch.cuda.stream(stream[i]):
            edge_block = edges[i * block_size:(i + 1) * block_size] if i < num_parallel - 1 else edges[i * block_size:]
            results[i] = torch.stack([torch.tensor(heur(u, v, M, models, entity_to_id_map, relation_to_id_map, all_triples_set, full_graph, sample, datasetname), device='cuda') for u, v in edge_block])

    # Wait for the computation to finish
    torch.cuda.synchronize()

    #prof.export_chrome_trace("trace.json")
    
    for i in range(num_parallel):
        sib_sum += torch.sum(results[i][:, 0])
        sib_sum_h += torch.sum(results[i][:, 1])
        sib_sum_t += torch.sum(results[i][:, 2])
    count = len_edges

    end_uv = timeit.default_timer()
    print(f'have done subgraph: {id(subgraph)} in {end_uv - start_uv}')

    sib_sum = sib_sum/count
    sib_sum_h = sib_sum_h/count
    sib_sum_t = sib_sum_t/count
    model_ReliK_score.append(sib_sum.item())
    model_ReliK_score_h.append(sib_sum_h.item())
    model_ReliK_score_t.append(sib_sum_t.item())
    tracker += 1
    if tracker % 10 == 0: print(f'have done subgraph: {tracker} of {len(subgraphs)} in {embedding}')
```

## Result:cry:

However, it seems that there is no speed up in the experiment, for some different `num_parallel` values. I suspect that it is executed sequentially. Maybe I have not thoroughly understood the code.

# :smiley:Third Try: `multiprocessing`

Idea is straight forward.

I have `num_processors` processors. Then I partition the edges `list(nx.DiGraph(M).subgraph(subgraph).edges())` into `num_processors` parts.

Each processor is responsible for a corresponding part.

```python
for subgraph in subgraphs:
    count = 0
    sib_sum = 0
    sib_sum_h = 0
    sib_sum_t = 0
    start_uv = timeit.default_timer()
    
    edges = list(nx.DiGraph(M).subgraph(subgraph).edges())
    count = len(edges)
    num_processors = 10

    chunk_size = len(edges) // num_processors

    edge_chunks = [edges[i * chunk_size:(i + 1) * chunk_size] if i < num_processors - 1 else edges[i * chunk_size:] for i in range(num_processors)]
    

    manager = mp.Manager()
    results = manager.list()

    processes = []
    for i,edge_chunk in enumerate(edge_chunks):
        p = mp.Process(target=process_edges_partition, args=(edge_chunk, heur, M, models, entity_to_id_map, relation_to_id_map, all_triples_set, full_graph, sample, datasetname, results))
        p.start()
        processes.append(p)
    
    for p in processes:
        p.join()
    
    sib_sum = sum([r[0] for r in results])
    sib_sum_h = sum([r[1] for r in results])
    sib_sum_t = sum([r[2] for r in results])
    #print(sib_sum, sib_sum_h, sib_sum_t)

    end_uv = timeit.default_timer()
    print(f'have done subgraph: {id(subgraph)} in {end_uv - start_uv}')

    sib_sum = sib_sum/count
    sib_sum_h = sib_sum_h/count
    sib_sum_t = sib_sum_t/count
    model_ReliK_score.append(sib_sum)
    model_ReliK_score_h.append(sib_sum_h)
    model_ReliK_score_t.append(sib_sum_t)
    tracker += 1
    if tracker % 10 == 0: print(f'have done {tracker} of {len(subgraphs)} in {embedding}')
```

I need to write the method `process_edges_partition` outside to process each part.

```python
def process_edges_partition(edge_partition, heur, M, models, entity_to_id_map, relation_to_id_map, all_triples_set, full_graph, sample, datasetname, results):
    #count = 0
    sib_sum = 0
    sib_sum_h = 0
    sib_sum_t = 0
    
    # Process each edge in the partition
    for u, v in edge_partition:
        w, w1, w2 = heur(u, v, M, models, entity_to_id_map, relation_to_id_map, all_triples_set, full_graph, sample, datasetname)
        #count += 1
        sib_sum += w
        sib_sum_h += w1
        sib_sum_t += w2

    results.append((sib_sum, sib_sum_h, sib_sum_t))
```

## Experiment🤩

I ran experiment on dataset `Countries` and `CodexSmall` respectively.

We want to speed up the process for calculating ReliK scores.

## Setting

Set `num_processors = 10`;

Using `TransE` embeddings.

###  Dataset - Countries

`python experiment_controller.py -d Countries -e TransE -t ReliK`

For raw implementation, it takes around **470 seconds**.

For multiprocessing optimization, it takes around **123 seconds**.

**Speedup: Around 3.8x**

### Dataset - CodexSmall

`python experiment_controller.py -d CodexSmall -e TransE -t ReliK`

For raw implementation, it takes around **12900 seconds** = 215 minutes ≈ 3.58 hours.

For multiprocessing optimization, it takes around **1610 second** ≈26.8 minutes:heavy_exclamation_mark::thumbsup:

**Speedup: Around 8.0x**

~~Staying up debugging until 2:30 a.m. finally paid off.~~:sunglasses: