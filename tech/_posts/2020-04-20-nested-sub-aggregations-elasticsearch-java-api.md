---
layout: post
title: "Nested Sub-Aggregations in Elasticsearch (Java API)"
date: 2020-04-20
categories: tech
author: "Akash Chatterjee"
---

This particular article is dedicated to a roadblock I faced when trying to work with the Elasticsearch aggregation APIs in Java.

Let's say you have an index named "manipal-university-index" in your Elasticsearch cluster. For the sake of simplicity, we shall not get into the concept of shards or pagination of data. We would assume that we will face no problem in terms of the response payload's size.

Now, in this "manipal-university-index", each document (representing one student) has 2 fields – "course_name" (E.g. Computer Science, Electronics etc.) and "section_name" (A, B, C etc.).

Let's say we want to aggregate and create buckets to count the total number of students per section per course.

**Note:** Here we want aggregation results in the response at each dimension level i.e. total count at 'course' level as well as total count at 'course + section' level. If you just want the grouped final bucket similar to a group by clause in SQL, go for 'Composite Aggregations'.

## Using the Elasticsearch REST API

If you were to perform this query using the Elasticsearch REST API, your request would look like this:

```json
GET manipal-university-index/_search
{
  "aggs": {
    "course_name": {
      "terms": {
        "field": "course_name"
      },
      "aggs": {
        "section_name": {
          "terms": {
            "field": "section_name"
          }
        }
      }
    }
  }
}
```

## Building Dynamic Nested Aggregations in Java

We need to achieve the same in Java. However, the aim is to not just reach two levels of nested aggregations but ensure that we can drill down to any level we want, dynamically.

The simple Java API to create an AggregationBuilder for one level is the following:

```java
AggregationBuilder aggregationBuilder = AggregationBuilders.terms("course_name").field("course_name")
```

We shall try to keep this format in mind and build a method that can process any number of parameters given to it in a String array and send back an AggregationBuilder object that gets our job done.

When the depth is unknown for a repeatable process, recursion is the simplest approach.

### Inputs

- **String[] parameters:** Buckets required from the nested aggregations, processed from index 0 -> N i.e. in the above example, parameter = ["course_name", "section_name"].
- **int depth:** The current depth of the recursion call. The first call should be made with depth = 0.

### The Solution

Here is the method:

```java
public AggregationBuilder nestAggregations(String[] parameters, int depth) {
    if (parameters.length == (depth + 1))
        return AggregationBuilders.terms(parameters[depth]).field(parameters[depth]);

    return AggregationBuilders
            .terms(parameters[depth])
            .field(parameters[depth])
            .subAggregation(nestAggregations(parameters, depth + 1));
}
```

## Nested Metric Aggregations with Averages

Similarly, we can also create other nested metric aggregations. Here, let's take the case of calculating average buckets across any given dimensions. We can assume we have a number field "score" and we want to calculate average of this field across all buckets and at each bucket level like before.

Your Elasticsearch REST API request would look something like this:

```json
GET manipal-university-index/_search
{
  "aggs": {
    "course_name": {
      "terms": {
        "field": "course_name"
      },
      "aggs": {
        "course_name_average": {
          "avg": {
            "field": "score"
          }
        },
        "section_name": {
          "terms": {
            "field": "section_name"
          },
          "aggs": {
            "section_name_average": {
              "avg": {
                "field": "score"
              }
            }
          }
        }
      }
    }
  }
}
```

To achieve this using a recursive method like before, we can do the following:

(Here, the 'averageMetricFieldName' is basically the field we're calculating the average of. In our case, the field 'score')

```java
public AggregationBuilder nestAverageAggregations(String[] dimensions, int depth, String averageMetricFieldName) {
    if (dimensions.length == (depth + 1))
        return AggregationBuilders
                .terms(dimensions[depth])
                .field(dimensions[depth])
                .size(10000)
                .subAggregation(AggregationBuilders.avg(dimensions[depth] + "_average").field(averageMetricFieldName));

    return AggregationBuilders
            .terms(dimensions[depth])
            .field(dimensions[depth])
            .subAggregation(AggregationBuilders.avg(dimensions[depth] + "_average").field(averageMetricFieldName))
            .subAggregation(nestAverageAggregations(dimensions, depth + 1, averageMetricFieldName))
            .size(10000);
}
```

Hope this helps you somewhere down the road.
