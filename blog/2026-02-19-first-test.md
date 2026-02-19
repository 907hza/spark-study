---
slug: first-test
title: "1주차: Spark DataFrame OOM 테스트 및 환경 세팅"
authors: [내아이디] # 본인 이름이나 닉네임
tags: [spark, scala, troubleshooting]
---

첫 번째 스터디 테스트 글입니다. 이번 주차에서는 Data Skew로 인한 메모리 초과 현상을 재현합니다.

### OOM 재현을 위한 Scala 테스트 코드

```scala
import org.apache.spark.sql.SparkSession
import org.apache.spark.sql.functions._

val spark = SparkSession.builder()
  .appName("SkewTest")
  .config("spark.memory.fraction", "0.2") // 메모리 압박을 위해 낮게 설정
  .getOrCreate()

import spark.implicits._

// 고의적인 Data Skew 생성
val df1 = Seq.fill(100000)("A").toDF("key") 
val df2 = Seq("A", "B", "C").toDF("key")

// 실행 시 Shuffle 병목 발생 유도
val joinedDF = df1.join(df2, "key")
joinedDF.show()