# analysis-tibetan

Tibetan analyzer for Opensearch and Elasticsearch

### Install

Copy Lucene-bo's [synonyms.txt](https://github.com/buda-base/lucene-bo/blob/master/src/main/resources/synonyms.txt) into `/etc/opensearch/tibetan-synonyms.txt`

### Getting started

Simple example:

```
POST _analyze
{
  "tokenizer" : "tibetan",
  "filter" : ["tibetan"],
  "char_filter" : ["tibetan"],
  "text" : "ཀ་ཁཱ་ག"
}
```

Example with parameters: first let's create an index for the test

```
PUT /tibetantest/
{
  "settings": {
    "analysis": {
      "analyzer": {
        "tibetan-lenient": {
          "tokenizer": "tibetan",
          "filter": [ "tibetan-lenient", "tibetan-synonyms" ],
          "char_filter": [ "tibetan-lenient" ]
        },
        "tibetan-ewts-lenient": {
          "tokenizer": "tibetan",
          "filter": [ "tibetan-lenient", "tibetan-synonyms" ],
          "char_filter": [ "tibetan-ewts-lenient" ]
        }
      },
      "filter": {
        "tibetan-lenient": {
          "type": "tibetan",
          "remove_affixes": true,
          "normalize_paba": true
        },
        "tibetan-synonyms": {
          "type": "synonym_graph",
          "synonyms_path": "tibetan-synonyms.txt"
        }
      },
      "char_filter": {
        "tibetan-lenient": {
          "type": "tibetan",
          "lenient": true
        },
        "tibetan-ewts-lenient": {
          "type": "tibetan",
          "lenient": true,
          "input_method": "ewts"
        }
      }
    }
  }
}
```

then test the lenient support (note that `ཁཱ` is transformed into `ཁ`):

```
{
  "tokenizer" : "tibetan",
  "filter" : ["tibetan-lenient", "tibetan-synonyms"],
  "char_filter" : ["tibetan-lenient"],
  "text" : "ཀ་ཁཱ་ག་ཀླད་ཀོར"
}
```

and the transliteration support:

```
POST /tibetantest/_analyze
{
  "tokenizer" : "tibetan",
  "filter" : ["tibetan-lenient", "tibetan-synonyms"],
  "char_filter" : ["tibetan-ewts-lenient"],
  "text" : "ka khA ga klad kor"
}
```

### Sources of inspiration and doc

https://github.com/lionsoul2014/jcseg
https://github.com/infinilabs/analysis-stconvert/

https://docs.aws.amazon.com/opensearch-service/latest/developerguide/custom-packages.html
