---
title: Efficient ReliK 4 Sampling Optimization
author: tinsir888
date: 2024/11/7
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
abbrlink: f0984699
---

# What is `heur`?

By default, `heur` is set to `binomial`.

```python
def binomial(u: str, v: str, M: nx.MultiDiGraph, models: list[object], entity_to_id_map: object, relation_to_id_map: object, all_triples_set: set[tuple[int,int,int]], alltriples: TriplesFactory, sample: float, dataset: str) -> float:
    '''
    get approximate ReliK score with binomial approximation
    '''
    #list(map(random.choice, map(list, list_of_sets)))

    global getkHopneighbors_time
    global sample_time
    global model_loop_time
    global sample_first_while_time
    global sample_second_while_time

    start_time = timeit.default_timer() # profiling 1
    subgraph_list, labels, existing, count, ex_triples  = dh.getkHopneighbors(u,v,M)
    end_time = timeit.default_timer() # profiling 1
    getkHopneighbors_time += end_time - start_time # profiling 1

    start_time = timeit.default_timer() # profiling 2
    #print(entity_to_id_map)
    #subgraph_list, labels, existing, count, ex_triples  = dh.getkHopneighbors(entity_to_id_map[u],entity_to_id_map[v],M)
    if sample > 0.4:
        allset_uu = set(itertools.product([entity_to_id_map[u]],range(alltriples.num_relations),range(alltriples.num_entities)))
        allset_vv = set(itertools.product(range(alltriples.num_entities),range(alltriples.num_relations),[entity_to_id_map[v]]))
        len_uu = len(allset_uu.difference(all_triples_set))
        len_vv = len(allset_vv.difference(all_triples_set))

        allset = set()
        allset_u = set()
        allset_v = set()
        
        lst_emb = list(range(alltriples.num_entities))
        lst_emb_r = list(range(alltriples.num_relations))

        first = True
        count = 0
        while len(allset_u) < min(len_uu*sample,1000):
            #count += 1
            relation = random.choice(lst_emb_r)
            tail = random.choice(lst_emb)
            kg_neg_triple_tuple = (entity_to_id_map[u],relation,tail)
            if kg_neg_triple_tuple not in all_triples_set and kg_neg_triple_tuple not in allset_u:
                if first:
                    first = False
                    rslt_torch_u = torch.LongTensor([entity_to_id_map[u],relation,tail])
                    rslt_torch_u = rslt_torch_u.resize_(1,3)
                else:
                    rslt_torch_u = torch.cat((rslt_torch_u, torch.LongTensor([entity_to_id_map[u],relation,tail]).resize_(1,3)))
                allset_u.add(kg_neg_triple_tuple)
            else:
                count += 1
            #if count == len_uu*sample:#min(len_uu*sample,1000):
            #    break
        count = 0
        first = True
        while len(allset_v) < min(len_vv*sample,1000):
            #count += 1
            relation = random.choice(lst_emb_r)
            head = random.choice(lst_emb)
            kg_neg_triple_tuple = (head,relation,entity_to_id_map[v])
            if kg_neg_triple_tuple not in all_triples_set and kg_neg_triple_tuple not in allset_v:
                if first:
                    first = False
                    rslt_torch_v = torch.LongTensor([head,relation,entity_to_id_map[v]])
                    rslt_torch_v = rslt_torch_v.resize_(1,3)
                else:
                    rslt_torch_v = torch.cat((rslt_torch_v, torch.LongTensor([head,relation,entity_to_id_map[v]]).resize_(1,3)))
                allset_v.add(kg_neg_triple_tuple)
            else:
                count += 1
    else:
        allset_u = set()
        allset_v = set()
        len_uu = alltriples.num_entities*alltriples.num_relations
        len_vv = alltriples.num_entities*alltriples.num_relations
        first = True
        start_first_while = timeit.default_timer() # profiling 4
        while len(allset_u) < min(len_uu*sample,1000):
            kg_neg_triple_tuple = tuple(map(random.choice, map(list, [range(alltriples.num_relations),range(alltriples.num_entities)] )))
            # optimization: sampling without replacement
            kg_neg_triple_tuple = (entity_to_id_map[u], kg_neg_triple_tuple[0], kg_neg_triple_tuple[1])
            if kg_neg_triple_tuple not in all_triples_set and kg_neg_triple_tuple not in allset_u:
                if first:
                    first = False
                    rslt_torch_u = torch.LongTensor([kg_neg_triple_tuple[0],kg_neg_triple_tuple[1],kg_neg_triple_tuple[2]])
                    rslt_torch_u = rslt_torch_u.resize_(1,3)
                else:
                    rslt_torch_u = torch.cat((rslt_torch_u, torch.LongTensor([kg_neg_triple_tuple[0],kg_neg_triple_tuple[1],kg_neg_triple_tuple[2]]).resize_(1,3)))
                allset_u.add(kg_neg_triple_tuple)

        end_first_while = timeit.default_timer() # profiling 4
        sample_first_while_time += end_first_while - start_first_while # profiling 4

        first = True

        start_second_while = timeit.default_timer() # profiling 5

        while len(allset_v) < min(len_vv*sample,1000):
            kg_neg_triple_tuple = tuple(map(random.choice, map(list, [range(alltriples.num_relations),range(alltriples.num_entities)] )))
            kg_neg_triple_tuple = (kg_neg_triple_tuple[1], kg_neg_triple_tuple[0], entity_to_id_map[v])
            if kg_neg_triple_tuple not in all_triples_set and kg_neg_triple_tuple not in allset_u:
                if first:
                    first = False
                    rslt_torch_v = torch.LongTensor([kg_neg_triple_tuple[0],kg_neg_triple_tuple[1],kg_neg_triple_tuple[2]])
                    rslt_torch_v = rslt_torch_u.resize_(1,3)
                else:
                    rslt_torch_v = torch.cat((rslt_torch_u, torch.LongTensor([kg_neg_triple_tuple[0],kg_neg_triple_tuple[1],kg_neg_triple_tuple[2]]).resize_(1,3)))
                allset_v.add(kg_neg_triple_tuple)

        end_second_while = timeit.default_timer() # profiling 5
        sample_second_while_time += end_second_while - start_second_while # profiling 5


    end_time = timeit.default_timer() # profiling 2
    sample_time += end_time - start_time # profiling 2

    first = True
    for tp in list(existing):
        if first:
            first = False
            ex_torch = torch.LongTensor([entity_to_id_map[u],relation_to_id_map[tp],entity_to_id_map[v]])
            ex_torch = ex_torch.resize_(1,3)
        else:
            ex_torch = torch.cat((ex_torch, torch.LongTensor([entity_to_id_map[u],relation_to_id_map[tp],entity_to_id_map[v]]).resize_(1,3)))

    hRankNeg = 0
    tRankNeg = 0


    start_time = timeit.default_timer() # profiling 3
    for i in range(len(models)):
        comp_score = models[i].score_hrt(ex_torch).cpu()
        rslt_u_score = models[i].score_hrt(rslt_torch_u)
        rslt_v_score = models[i].score_hrt(rslt_torch_v)
        count = 0
        he_sc = 0
        ta_sc = 0
        for tr in comp_score:
            count += 1
            he_sc += torch.sum(rslt_u_score > tr).detach().numpy() + 1
            ta_sc += torch.sum(rslt_v_score > tr).detach().numpy() + 1
        hRankNeg += ((he_sc / len(allset_u))/len(models)) * len_uu
        tRankNeg += ((ta_sc / len(allset_v))/len(models)) * len_vv
    end_time = timeit.default_timer() # profiling 3
    model_loop_time += end_time - start_time # profiling 3

    return ( 1/hRankNeg + 1/tRankNeg )/2, 1/hRankNeg, 1/tRankNeg
```

So I plug in some time measure on several parts of `binomial`.

It seems that the two sampling parts (generating negative sample for head and tail respectively) take most of running time.

So I consider working on optimization for sampling.

Next we need a more fine-grained profiling with sampling loop!

# Optimization on Sampling

It seems that `random.choice` takes some time.

More importantly, in `while` loop, there exist `if` branch, which is the bottle-neck.

We need to break these.

So we adapt to another sampling approach:

1. Generating random permutation for entity id and relation id, respectively.
2. Use `torch.stack` to make up sampling triple **without checking its negativity**.
3. Use some parallelization methods to calculate relative complement set.

Then we have a sample that contains only negative triples.

# Experiment Results

## Raw Implementation

### Dataset Countries

`python experiment_controller.py -d Countries -e TransE -t ReliK`

```
Total time for dh.getkHopneighbors: 3.1996391420980217 seconds
Total time for sampling: 168.68687357635645 seconds
Total time for model loop: 361.5889958173502 seconds
Total time for sample first while loop: 89.01678210946557 seconds
Total time for sample second while loop: 79.46071726566879 seconds
```

Total running time: 538 seconds

### Dataset CodexSmall

`nohup python experiment_controller.py -d CodexSmall -e TransE -t ReliK > SEQ_raw_codexsmall.log 2>&1 &`

```
Total time for dh.getkHopneighbors: 57.30740173064987 seconds
Total time for sampling: 11898.253619632305 seconds
Total time for model loop: 1441.337782963441 seconds
Total time for sample first while loop: 6044.276725897187 seconds
Total time for sample second while loop: 5853.295521991327 seconds
```

Total running time: 13471 seconds

## Optimized Sampling

### Dataset Countries

`python experiment_controller.py -d Countries -e TransE -t ReliK -heur binomial-cuda`

```
Total time for dh.getkHopneighbors: 3.3007240201113746 seconds
Total time for sampling: 113.55880117698689 seconds
Total time for model loop: 525.0190924630442 seconds
Total time for sample first while loop: 59.88825342673226 seconds
Total time for sample second while loop: 53.34763718127215 seconds
```

Total running time: 648 seconds.

### Dataset CodexSmall

`nohup python experiment_controller.py -d CodexSmall -e TransE -t ReliK -heur binomial-cuda > SEQ_opt_sample_codexsmall.log 2>&1 &`

```
Total time for dh.getkHopneighbors: 57.000073540984886 seconds
Total time for sampling: 266.82070101347927 seconds
Total time for model loop: 1514.4041375433735 seconds
Total time for sample first while loop (optimized): 138.913820838221 seconds
Total time for sample second while loop (optimized): 127.24364097954822 seconds
```

Total running time: 1895 seconds

## Analyzing

In `Countries` dataset, we see the general time is worse off.

Whereas in `CodexSmall`, there is an approx **7x speed-up**.

# What's Next?

## Experiment

Calculate the MSE loss between optimized approximate ReliK score and original true ReliK score. (with different sampling rate)

Compare and poly the **time** for calculation of optimized approximate ReliK score. (with different sampling rate)

## Problem

Can it be possible to combine parallel for-loop (CPU multi-process) and parallel sampling (GPU cuda)?

Theoretically yes, but currently it fails since for a single process, it can not re-initialize CUDA. (Runtime Error)