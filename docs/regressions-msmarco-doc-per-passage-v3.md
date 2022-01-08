# Anserini: Regressions for MS MARCO Document Ranking

This page documents regression experiments for the [MS MARCO document ranking task](https://github.com/microsoft/MSMARCO-Document-Ranking), which is integrated into Anserini's regression testing framework.
Note that there are four different regression conditions for this task, and this page describes the following:

+ **Indexing Condition:** each MS MARCO document is first segmented into passages, each passage is treated as a unit of indexing
+ **Expansion Condition:** none

In the passage indexing condition, we select the score of the highest-scoring passage from a document as the score for that document to produce a document ranking; this is known as the MaxP technique.
All four conditions are described in detail [here](https://github.com/castorini/docTTTTTquery#reproducing-ms-marco-document-ranking-results-with-anserini), in the context of doc2query-T5.

**NOTE**: This is the `msmarco-doc-per-passage-v3` variant (there's also `msmarco-doc-per-passage` and `msmarco-doc-per-passage-v2`), see [this page](experiments-msmarco-doc-doc2query-details.md) for detailed notes about differences between these variants.

The exact configurations for these regressions are stored in [this YAML file](../src/main/resources/regression/msmarco-doc-per-passage-v3.yaml).
Note that this page is automatically generated from [this template](../src/main/resources/docgen/templates/msmarco-doc-per-passage-v3.template) as part of Anserini's regression pipeline, so do not modify this page directly; modify the template instead.

## Indexing

Typical indexing command:

```
target/appassembler/bin/IndexCollection \
  -collection JsonCollection \
  -input /path/to/msmarco-doc-per-passage-v3 \
  -index indexes/lucene-index.msmarco-doc-per-passage-v3 \
  -generator DefaultLuceneDocumentGenerator \
  -threads 16 -storePositions -storeDocvectors -storeRaw \
  >& logs/log.msmarco-doc-per-passage-v3 &
```

The directory `/path/to/msmarco-doc-per-passage/` should be a directory containing the segmented paragraph collection; see [this link](https://github.com/castorini/docTTTTTquery#reproducing-ms-marco-document-ranking-results-with-anserini) for how to prepare this collection.

For additional details, see explanation of [common indexing options](common-indexing-options.md).

## Retrieval

Topics and qrels are stored in [`src/main/resources/topics-and-qrels/`](../src/main/resources/topics-and-qrels/).
The regression experiments here evaluate on the 5193 dev set questions.

After indexing has completed, you should be able to perform retrieval as follows:

```
target/appassembler/bin/SearchCollection \
  -index indexes/lucene-index.msmarco-doc-per-passage-v3 \
  -topics src/main/resources/topics-and-qrels/topics.msmarco-doc.dev.txt -topicreader TsvInt \
  -output runs/run.msmarco-doc-per-passage-v3.bm25-default.topics.msmarco-doc.dev.txt \
  -bm25 -hits 10000 -selectMaxPassage -selectMaxPassage.delimiter "#" -selectMaxPassage.hits 1000 &

target/appassembler/bin/SearchCollection \
  -index indexes/lucene-index.msmarco-doc-per-passage-v3 \
  -topics src/main/resources/topics-and-qrels/topics.msmarco-doc.dev.txt -topicreader TsvInt \
  -output runs/run.msmarco-doc-per-passage-v3.bm25-default+rm3.topics.msmarco-doc.dev.txt \
  -bm25 -rm3 -hits 10000 -selectMaxPassage -selectMaxPassage.delimiter "#" -selectMaxPassage.hits 1000 &

target/appassembler/bin/SearchCollection \
  -index indexes/lucene-index.msmarco-doc-per-passage-v3 \
  -topics src/main/resources/topics-and-qrels/topics.msmarco-doc.dev.txt -topicreader TsvInt \
  -output runs/run.msmarco-doc-per-passage-v3.bm25-default+ax.topics.msmarco-doc.dev.txt \
  -bm25 -axiom -axiom.deterministic -rerankCutoff 20 -hits 10000 -selectMaxPassage -selectMaxPassage.delimiter "#" -selectMaxPassage.hits 1000 &

target/appassembler/bin/SearchCollection \
  -index indexes/lucene-index.msmarco-doc-per-passage-v3 \
  -topics src/main/resources/topics-and-qrels/topics.msmarco-doc.dev.txt -topicreader TsvInt \
  -output runs/run.msmarco-doc-per-passage-v3.bm25-default+prf.topics.msmarco-doc.dev.txt \
  -bm25 -bm25prf -hits 10000 -selectMaxPassage -selectMaxPassage.delimiter "#" -selectMaxPassage.hits 1000 &

target/appassembler/bin/SearchCollection \
  -index indexes/lucene-index.msmarco-doc-per-passage-v3 \
  -topics src/main/resources/topics-and-qrels/topics.msmarco-doc.dev.txt -topicreader TsvInt \
  -output runs/run.msmarco-doc-per-passage-v3.bm25-tuned.topics.msmarco-doc.dev.txt \
  -bm25 -bm25.k1 2.16 -bm25.b 0.61 -hits 10000 -selectMaxPassage -selectMaxPassage.delimiter "#" -selectMaxPassage.hits 1000 &

target/appassembler/bin/SearchCollection \
  -index indexes/lucene-index.msmarco-doc-per-passage-v3 \
  -topics src/main/resources/topics-and-qrels/topics.msmarco-doc.dev.txt -topicreader TsvInt \
  -output runs/run.msmarco-doc-per-passage-v3.bm25-tuned+rm3.topics.msmarco-doc.dev.txt \
  -bm25 -bm25.k1 2.16 -bm25.b 0.61 -rm3 -hits 10000 -selectMaxPassage -selectMaxPassage.delimiter "#" -selectMaxPassage.hits 1000 &

target/appassembler/bin/SearchCollection \
  -index indexes/lucene-index.msmarco-doc-per-passage-v3 \
  -topics src/main/resources/topics-and-qrels/topics.msmarco-doc.dev.txt -topicreader TsvInt \
  -output runs/run.msmarco-doc-per-passage-v3.bm25-tuned+ax.topics.msmarco-doc.dev.txt \
  -bm25 -bm25.k1 2.16 -bm25.b 0.61 -axiom -axiom.deterministic -rerankCutoff 20 -hits 10000 -selectMaxPassage -selectMaxPassage.delimiter "#" -selectMaxPassage.hits 1000 &

target/appassembler/bin/SearchCollection \
  -index indexes/lucene-index.msmarco-doc-per-passage-v3 \
  -topics src/main/resources/topics-and-qrels/topics.msmarco-doc.dev.txt -topicreader TsvInt \
  -output runs/run.msmarco-doc-per-passage-v3.bm25-tuned+prf.topics.msmarco-doc.dev.txt \
  -bm25 -bm25.k1 2.16 -bm25.b 0.61 -bm25prf -hits 10000 -selectMaxPassage -selectMaxPassage.delimiter "#" -selectMaxPassage.hits 1000 &
```

Evaluation can be performed using `trec_eval`:

```
tools/eval/trec_eval.9.0.4/trec_eval -c -m map -c -m recall.100 -c -m recall.1000 src/main/resources/topics-and-qrels/qrels.msmarco-doc.dev.txt runs/run.msmarco-doc-per-passage-v3.bm25-default.topics.msmarco-doc.dev.txt

tools/eval/trec_eval.9.0.4/trec_eval -c -m map -c -m recall.100 -c -m recall.1000 src/main/resources/topics-and-qrels/qrels.msmarco-doc.dev.txt runs/run.msmarco-doc-per-passage-v3.bm25-default+rm3.topics.msmarco-doc.dev.txt

tools/eval/trec_eval.9.0.4/trec_eval -c -m map -c -m recall.100 -c -m recall.1000 src/main/resources/topics-and-qrels/qrels.msmarco-doc.dev.txt runs/run.msmarco-doc-per-passage-v3.bm25-default+ax.topics.msmarco-doc.dev.txt

tools/eval/trec_eval.9.0.4/trec_eval -c -m map -c -m recall.100 -c -m recall.1000 src/main/resources/topics-and-qrels/qrels.msmarco-doc.dev.txt runs/run.msmarco-doc-per-passage-v3.bm25-default+prf.topics.msmarco-doc.dev.txt

tools/eval/trec_eval.9.0.4/trec_eval -c -m map -c -m recall.100 -c -m recall.1000 src/main/resources/topics-and-qrels/qrels.msmarco-doc.dev.txt runs/run.msmarco-doc-per-passage-v3.bm25-tuned.topics.msmarco-doc.dev.txt

tools/eval/trec_eval.9.0.4/trec_eval -c -m map -c -m recall.100 -c -m recall.1000 src/main/resources/topics-and-qrels/qrels.msmarco-doc.dev.txt runs/run.msmarco-doc-per-passage-v3.bm25-tuned+rm3.topics.msmarco-doc.dev.txt

tools/eval/trec_eval.9.0.4/trec_eval -c -m map -c -m recall.100 -c -m recall.1000 src/main/resources/topics-and-qrels/qrels.msmarco-doc.dev.txt runs/run.msmarco-doc-per-passage-v3.bm25-tuned+ax.topics.msmarco-doc.dev.txt

tools/eval/trec_eval.9.0.4/trec_eval -c -m map -c -m recall.100 -c -m recall.1000 src/main/resources/topics-and-qrels/qrels.msmarco-doc.dev.txt runs/run.msmarco-doc-per-passage-v3.bm25-tuned+prf.topics.msmarco-doc.dev.txt
```

## Effectiveness

With the above commands, you should be able to reproduce the following results:

MAP                                     | BM25 (default)| +RM3      | +Ax       | +PRF      | BM25 (tuned)| +RM3      | +Ax       | +PRF      |
:---------------------------------------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|
[MS MARCO Doc: Dev](https://github.com/microsoft/MSMARCO-Document-Ranking)| 0.2690    | 0.2419    | 0.2208    | 0.2325    | 0.2762    | 0.2450    | 0.2330    | 0.2276    |


R@100                                   | BM25 (default)| +RM3      | +Ax       | +PRF      | BM25 (tuned)| +RM3      | +Ax       | +PRF      |
:---------------------------------------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|
[MS MARCO Doc: Dev](https://github.com/microsoft/MSMARCO-Document-Ranking)| 0.7847    | 0.7882    | 0.7710    | 0.7722    | 0.8013    | 0.7961    | 0.7888    | 0.7687    |


R@1000                                  | BM25 (default)| +RM3      | +Ax       | +PRF      | BM25 (tuned)| +RM3      | +Ax       | +PRF      |
:---------------------------------------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|
[MS MARCO Doc: Dev](https://github.com/microsoft/MSMARCO-Document-Ranking)| 0.9178    | 0.9355    | 0.9264    | 0.9185    | 0.9311    | 0.9363    | 0.9353    | 0.9157    |

Explanation of settings:

+ The setting "default" refers the default BM25 settings of `k1=0.9`, `b=0.4`.
+ The setting "tuned" refers to `k1=2.16`, `b=0.61`, tuned to optimize for recall@100 (i.e., for first-stage retrieval) on 2019/12.

In these runs, we are retrieving the top 1000 hits for each query and using `trec_eval` to evaluate all 1000 hits.
Since we're in the passage condition, we fetch the 10000 passages and select the top 1000 documents using MaxP.
This lets us measure R@100 and R@1000; the latter is particularly important when these runs are used as first-stage retrieval.
Beware, an official MS MARCO document ranking task leaderboard submission comprises only 100 hits per query.
See [this page](experiments-msmarco-doc-leaderboard.md) for details on Anserini baseline runs that were submitted to the official leaderboard.

The MaxP passage retrieval functionality is available in `SearchCollection`.
To generate an MS MARCO submission with the BM25 default parameters, corresponding to "BM25 (default)" above:

```bash
$ target/appassembler/bin/SearchCollection -topicreader TsvString \
   -topics src/main/resources/topics-and-qrels/topics.msmarco-doc.dev.txt \
   -index indexes/lucene-index.msmarco-doc-per-passage-v3.pos+docvectors+raw \
   -output runs/run.msmarco-doc-per-passage-v3.bm25-default.txt -format msmarco \
   -bm25 -bm25.k1 0.9 -bm25.b 0.4 -hits 1000 \
   -selectMaxPassage -selectMaxPassage.delimiter "#" -selectMaxPassage.hits 100

$ python tools/scripts/msmarco/msmarco_doc_eval.py \
   --judgments src/main/resources/topics-and-qrels/qrels.msmarco-doc.dev.txt \
   --run runs/run.msmarco-doc-per-passage-v3.bm25-default.txt

#####################
MRR @100: 0.26851990908986706
QueriesRanked: 5193
#####################
```

Note that the above command uses `-format msmarco` to directly generate a run in the MS MARCO output format.

To generate an MS MARCO submission with the BM25 tuned parameters, corresponding to "BM25 (tuned)" above:

```bash
$ target/appassembler/bin/SearchCollection -topicreader TsvString \
   -topics src/main/resources/topics-and-qrels/topics.msmarco-doc.dev.txt \
   -index indexes/lucene-index.msmarco-doc-per-passage-v3.pos+docvectors+raw \
   -output runs/run.msmarco-doc-per-passage-v3.bm25-tuned.txt -format msmarco \
   -bm25 -bm25.k1 2.16 -bm25.b 0.61 -hits 1000 \
   -selectMaxPassage -selectMaxPassage.delimiter "#" -selectMaxPassage.hits 100

$ python tools/scripts/msmarco/msmarco_doc_eval.py \
   --judgments src/main/resources/topics-and-qrels/qrels.msmarco-doc.dev.txt \
   --run runs/run.msmarco-doc-per-passage-v3.bm25-tuned.txt

#####################
MRR @100: 0.27551963417683756
QueriesRanked: 5193
#####################
```

Again, note that the above command uses `-format msmarco` to directly generate a run in the MS MARCO output format.