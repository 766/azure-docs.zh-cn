---
title: 将 MapReduce 与 HDInsight 上的 Apache Hadoop 配合使用
description: 了解如何在 Apache Hadoop in HDInsight 群集中运行 MapReduce 作业。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 9da6b6ba3ab697887e55f9077b44cf6fa100a981
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64707968"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>在 Apache Hadoop on HDInsight 中使用 MapReduce

了解如何在 HDInsight 群集上运行 MapReduce 作业。 

## <a id="whatis"></a>什么是 MapReduce

Apache Hadoop MapReduce 是一个软件框架，用于编写处理海量数据的作业。 输入的数据将拆分为独立的区块。 每个区块跨群集中的节点并行进行处理。 MapReduce 作业包括两个函数：

* **映射器**：使用输入数据，对数据进行分析（通常使用筛选器和排序操作），并发出元组（键/值对）

* **化简器**：使用映射器发出的元组并执行汇总运算，以基于映射器数据创建更小的合并结果

下图演示了一个基本的单词计数 MapReduce 作业示例：

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

此作业的输出是文本中每个单词出现次数的计数。

* 映射器将输入文本中的每行用作一个输入并将其拆分为多个单词。 每当一个单词出现时，mapper 发出一个键/值对，其中在该单词后跟一个 1。 输出在发送到化简器之前经过排序。
* 随后，化简器会计算每个单词的计数的和并发出一个键/值对（包含单词，后跟该单词的总出现次数）。

MapReduce 可使用多种语言实现。 Java 是最常见的实现，本文档中使用该语言进行演示。

## <a name="development-languages"></a>开发语言

基于 Java 和 Java 虚拟机的语言或框架可作为 MapReduce 作业直接运行。 在本文档中使用的示例是 Java MapReduce 应用程序。 C#、Python 等非 Java 语言或独立可执行文件必须使用 **Hadoop 流式处理**。

Hadoop 流式处理通过 STDIN 和 STDOUT 与映射器和化简器通信。 映射器和化简器从 STDIN 中一次读取一行数据，并将输出写入 STDOUT。 映射器和化简器读取或发出的每行必须采用制表符分隔的键/值对格式：

    [key]/t[value]

有关详细信息，请参阅 [Hadoop Streaming](https://hadoop.apache.org/docs/r1.2.1/streaming.html)（Hadoop 流式处理）。

有关将 Hadoop 流式处理与 HDInsight 配合使用的示例，请参阅以下文档：

* [开发 C# MapReduce 作业](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a id="data"></a>示例数据

HDInsight 提供存储在 `/example/data` 和 `/HdiSamples` 目录中的各种示例数据集。 这些目录位于群集的默认存储中。 在本文档中，我们使用 `/example/data/gutenberg/davinci.txt` 文件。 此文件包含 Leonardo da Vinci 的笔记本。

## <a id="job"></a>MapReduce 示例

MapReduce 单词计数应用程序示例包含在 HDInsight 群集中。 此示例位于群集默认存储的 `/example/jars/hadoop-mapreduce-examples.jar` 中。

以下 Java 代码是包含在 `hadoop-mapreduce-examples.jar` 文件中的 MapReduce 应用程序的源代码：

```java
package org.apache.hadoop.examples;

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class WordCount {

    public static class TokenizerMapper
        extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
        StringTokenizer itr = new StringTokenizer(value.toString());
        while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
        }
    }
    }

    public static class IntSumReducer
        extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                        Context context
                        ) throws IOException, InterruptedException {
        int sum = 0;
        for (IntWritable val : values) {
        sum += val.get();
        }
        result.set(sum);
        context.write(key, result);
    }
    }

    public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
        System.err.println("Usage: wordcount <in> <out>");
        System.exit(2);
    }
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

有关编写自己的 MapReduce 应用程序的说明，请参阅以下文档：

* [为 HDInsight 开发 Java MapReduce 应用程序](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

## <a id="run"></a>运行 MapReduce

HDInsight 可以使用各种方法运行 HiveQL 作业。 使用下表来确定哪种方法最适合，并按链接进行演练。

| **请使用以下方法**... | **...要执行以下操作** | ...使用此**群集操作系统** | ...从此**客户端操作系统** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |通过 **SSH** 使用 Hadoop 命令 |Linux |Linux、Unix、Mac OS X 或 Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |使用 **REST** 远程提交作业 |Linux 或 Windows |Linux、Unix、Mac OS X 或 Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |使用 **Windows PowerShell** 远程提交作业 |Linux 或 Windows |Windows |

## <a id="nextsteps"></a>后续步骤

若要了解如何使用 HDInsight 中的数据的详细信息，请参阅以下文档：

* [为 HDInsight 开发 Java MapReduce 程序](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [将 Apache Hive 和 HDInsight 配合使用][hdinsight-use-hive]

* [将 Apache Pig 和 HDInsight 配合使用][hdinsight-use-pig]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-pig]:hdinsight-use-pig.md


[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif
