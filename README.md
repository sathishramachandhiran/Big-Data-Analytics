# EXP 4:Big Data Analytics
## Aim
To efficiently process large datasets using Apache Spark for transformations and Hadoop MapReduce for distributed data processing, demonstrating scalable and fault-tolerant big data analytics.
## Procedure
1. Initialize Spark and load the dataset into a DataFrame.
2. Perform transformations like filtering, grouping, and sorting using PySpark.
3. Write Mapper and Reducer classes for processing data with Hadoop MapReduce.
4. Compile the Java files into a JAR and execute the job on the Hadoop cluster.
5. Store data in HDFS to leverage distributed storage and fault tolerance.
6. Execute the Spark and Hadoop jobs, then collect and validate the results.
## PROGRAM AND OUTPUT:   
### A.Data Processing with Spark to Perform basic transformations and actions to Handle large datasets efficiently
Tools: Apache Spark
Python Code with Apache Spark (PySpark)
```py
from pyspark.sql import SparkSession
from pyspark.sql.functions import col, avg, count

# Initialize Spark session
spark = SparkSession.builder.appName("DataProcessingExample").getOrCreate()

# Sample data (large dataset simulation)
data = [
    ("John", 28, "M", 3000),
    ("Jane", 35, "F", 3500),
    ("Sam", 50, "M", 4000),
    ("Anna", 23, "F", 2800),
    ("Peter", 40, "M", 5000),
    ("Alice", 29, "F", 3200),
    ("Bob", 31, "M", 3300)
]

# Create DataFrame from the data
columns = ["Name", "Age", "Gender", "Salary"]
df = spark.createDataFrame(data, columns)

# Show the DataFrame
df.show()

# Basic Transformations and Actions

# 1. Filter - Get all males
males_df = df.filter(col("Gender") == "M")
males_df.show()

# 2. Group by Gender and calculate average salary
avg_salary_by_gender = df.groupBy("Gender").agg(avg("Salary").alias("AvgSalary"))
avg_salary_by_gender.show()

# 3. Count the number of people by Gender
count_by_gender = df.groupBy("Gender").agg(count("Name").alias("Count"))
count_by_gender.show()

# 4. Select specific columns and sort by salary
sorted_df = df.select("Name", "Salary").orderBy("Salary", ascending=False)
sorted_df.show()

# 5. Collect all records and print them (action)
records = df.collect()
print(records)

# Stop the Spark session
spark.stop()
```
### OUTPUT:
```
+-----+---+------+-----+
| Name|Age|Gender|Salary|
+-----+---+------+-----+
| John| 28|     M|  3000|
| Jane| 35|     F|  3500|
|  Sam| 50|     M|  4000|
| Anna| 23|     F|  2800|
|Peter| 40|     M|  5000|
|Alice| 29|     F|  3200|
|  Bob| 31|     M|  3300|
+-----+---+------+-----+

+-----+---+------+-----+
| Name|Age|Gender|Salary|
+-----+---+------+-----+
| John| 28|     M|  3000|
|  Sam| 50|     M|  4000|
|Peter| 40|     M|  5000|
|  Bob| 31|     M|  3300|
+-----+---+------+-----+

+------+----------+
|Gender| AvgSalary|
+------+----------+
|     M|    3575.0|
|     F|    3166.67|
+------+----------+

+------+-----+
|Gender|Count|
+------+-----+
|     M|    4|
|     F|    3|
+------+-----+

+-----+-----+
| Name|Salary|
+-----+-----+
|Peter|  5000|
|  Sam|  4000|
|  Bob|  3300|
|John |  3000|
|Alice|  3200|
|Jane |  3500|
|Anna |  2800|
+-----+-----+

[('John', 28, 'M', 3000), ('Jane', 35, 'F', 3500), ('Sam', 50, 'M', 4000), ('Anna', 23, 'F', 2800), ('Peter', 40, 'M', 5000), ('Alice', 29, 'F', 3200), ('Bob', 31, 'M', 3300)]
```

#### Explanation of the Operations:
1.Create DataFrame:<BR>
The createDataFrame() method is used to create a DataFrame from a list of tuples and column names.<BR>

2.Basic Transformations:
<BR>Filter: df.filter(col("Gender") == "M") filters the dataset to get only the male records.
<BR>GroupBy and Aggregation: We use groupBy() to group the dataset by the "Gender" column and then compute the average salary using agg() with avg().
<BR>Select and Sort: select() is used to choose specific columns, and orderBy() is used to sort the DataFrame based on the "Salary" column in descending order.
<BR>

3.Actions:
<BR>Show: The show() function prints the top rows of the DataFrame.
<BR>Collect: The collect() method is an action that retrieves the entire dataset from Spark's distributed storage back to the local machine.
<BR>

4.Spark Session:
<BR>The Spark session is initialized at the beginning of the script and stopped at the end to free up resources.

#### Explanation of the Output:
<BR>DataFrame: The first df.show() shows the entire dataset with Name, Age, Gender, and Salary columns.
<BR>Filtered Data: The males_df.show() shows only the records where the gender is "M".
<BR>Grouped Data: The avg_salary_by_gender.show() shows the average salary by gender. It aggregates the salary values by grouping the "Gender".
<BR>Count by Gender: The count_by_gender.show() counts how many people belong to each gender.
<BR>Sorted Data: The sorted_df.show() shows the Name and Salary columns sorted by salary in descending order.
<BR>Collect: The df.collect() action retrieves all the rows from the Spark DataFrame and prints them out.

### B.For Distributed Data Analysis to Process data using MapReduce and Understand distributed storage mechanisms
Tools: Hadoop (JAVA)
Perform distributed data processing with Hadoop, we typically use the MapReduce paradigm.
MapReduce involves two primary steps:

1.Map: Process input data and convert it into key-value pairs.

2.Reduce: Aggregate values based on keys.

Here’s an example of how you would write a MapReduce job in Hadoop using Java, which is the typical language used for Hadoop MapReduce programming.

Hadoop MapReduce Example (Word Count)

In this example, we will implement a simple Word Count program using MapReduce to count the occurrences of words in a text file. This program will be run on Hadoop to process the data in a distributed fashion.
#### Step 1: Mapper Class
The Mapper class takes input from the file, processes it, and outputs key-value pairs (word, 1).
```java
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Mapper;
import java.io.IOException;

public class WordCountMapper extends Mapper<Object, Text, Text, IntWritable> {

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    @Override
    public void map(Object key, Text value, Context context) throws IOException, InterruptedException {
        // Split the line into words
        String[] words = value.toString().split("\\s+");
        
        // For each word, emit a key-value pair (word, 1)
        for (String wordText : words) {
            word.set(wordText);
            context.write(word, one);
        }
    }
}
```
#### Step 2: Reducer Class
The Reducer class takes the key-value pairs output by the Mapper, aggregates the values by key, and outputs the word with the total count.
```java 
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Reducer;
import java.io.IOException;

public class WordCountReducer extends Reducer<Text, IntWritable, Text, IntWritable> {

    private IntWritable result = new IntWritable();

    @Override
    public void reduce(Text key, Iterable<IntWritable> values, Context context) throws IOException, InterruptedException {
        int sum = 0;
        
        // Sum up the occurrences of each word
        for (IntWritable val : values) {
            sum += val.get();
        }
        
        result.set(sum);
        context.write(key, result);  // Emit (word, count)
    }
}
```
#### Step 3: Driver Class
```java
The Driver class sets up the configuration, specifies the input and output paths, and executes the MapReduce job.
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class WordCount {

    public static void main(String[] args) throws Exception {
        // Check if the correct number of arguments is passed
        if (args.length != 2) {
            System.err.println("Usage: WordCount <input path> <output path>");
            System.exit(-1);
        }

        // Create a new configuration object
        Configuration conf = new Configuration();

        // Create a job instance
        Job job = Job.getInstance(conf, "Word Count");

        // Set the Jar file that contains this driver class
        job.setJarByClass(WordCount.class);

        // Set the Mapper and Reducer classes
        job.setMapperClass(WordCountMapper.class);
        job.setReducerClass(WordCountReducer.class);

        // Set the output types
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);

        // Set the input and output paths
        FileInputFormat.addInputPath(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));

        // Run the job and wait for completion
        System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```
#### Step 4: Compile and Run the Job
1.Compile the Java classes into a .jar file.
```javac
javac -classpath `hadoop classpath` -d /path/to/output/WordCount/ WordCountMapper.java WordCountReducer.java WordCount.javajar -cvf WordCount.jar -C /path/to/output/WordCount/ .
```
Submit the job to Hadoop:

Once you have the .jar file, you can submit the job to the Hadoop cluster with the following command:
hadoop jar WordCount.jar WordCount /input/path /output/path
This will process the data in the /input/path directory, apply the MapReduce job, and output the results to /output/path.
#### Step 5: Output
The output of the job will be saved in the output path. Each line in the output will contain a word followed by the number of occurrences in the input text.
```
Example output in HDFS (Hadoop Distributed File System):
the   10
hello 5
world 8
hadoop 7
is 3
```
### Explanation of the Process:
1.Map Phase:

The Mapper takes each line of input, splits it into words, and emits each word as a key with the value 1 (count of occurrences).

2.Shuffle and Sort Phase:

Hadoop automatically groups all the values by key (word). So, all occurrences of the same word are sent to the same Reducer.

3.Reduce Phase:

The Reducer aggregates the counts for each word and emits the final word count as the output.

### Understanding Distributed Storage Mechanisms:
In Hadoop, data is stored in the Hadoop Distributed File System (HDFS). HDFS divides data into blocks (usually 128MB or 256MB in size) and stores these blocks across multiple nodes in the cluster. This enables high availability and fault tolerance.

Block Replication: HDFS replicates each block of data to multiple nodes (usually 3 copies) to ensure that the data is not lost even if a node fails.

Data Locality: Hadoop tries to process the data on the same node where it is stored, which helps improve performance by reducing the network load.

### Key Benefits of Hadoop and MapReduce:

Scalability: Hadoop can scale to handle petabytes of data by adding more nodes to the cluster.

Fault Tolerance: Due to block replication, even if a node fails, the data can still be processed from other nodes.

Parallel Processing: MapReduce jobs are executed in parallel on multiple nodes, making data processing faster for large datasets.

This MapReduce job demonstrates how to process large datasets efficiently in a distributed environment using Hadoop. It takes advantage of distributed storage (HDFS) and parallel processing (MapReduce) to process data across multiple machines, making it suitable for big data applications.

## Result

The dataset was processed efficiently using Apache Spark and Hadoop MapReduce, demonstrating scalable and fault-tolerant big data processing.

