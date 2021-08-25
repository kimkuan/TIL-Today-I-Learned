### Linux 디렉토리

- 다운받은 `Project.tar.gz`의 압축을 풀면 다음과 같은 폴더 구조를 확인할 수 있다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/665956c8-bb41-4a7f-9572-1d8a74ca5f5e/Untitled.png)

```markdown
- src : 실제 코드를 넣는 디렉토리 
	- Driver.java : 맵 리듀스 코드를 컴파일 하기 위한 파일
  - Wordcount.java : 맵 리듀스 코드 

- template : 템플릿을 저장하는 디렉토리
- datagen : 데이터를 생성하기 위한 코드
- date : 데이터를 저장 ex) wordcount_data.txt
- build.xml : 맵 리듀스 코드를 컴파일 하기 위한 코드
```

### Hadoop 디렉토리 (= HDFS)

```markdown
- wordcount_test : 맵 리듀스 코드 실행을 위한 데이터 디렉토리
- wordcount_test_out : 맵 리듀스 코드 실행 결과를 저장하는 디렉토리
```

### Hadoop 실행을 위한 준비

```bash
$ cd /home/hadoop
$ source .bashrc // Path를 지정하기 위한 명령어
$ hadoop namenode -format 

$ start-dfs.sh // dfs 데몬 실행
$ start-mapred.sh // mapReduce 데몬 실행 (단, standalone 모드에서는 불필요) 

# stop-dfs.sh // dfs 데몬 중지
# stop-mapred.sh // mapReduce 데몬 중지

$ jps // 수행중인 java 프로세스 리스트를 확인한다
	- DataNode
	- SecondaryNameNode
	- NameNode
	- Jps
	- TaskTracker (Standalone에서는 불필요)
	- JobTracker (Standalone에서는 불필요)

# DataNode가 실행되지 않는 오류?!
$ stop-dfs.sh
$ cd hadoop/logs
$ cat hadoop-hadoop-datanode-ubuntu.log // Incompatible Cluster ID ~

$ cd hadoop/bin
$ ./hdfs namenode -format -clusterId CID-8bf63244-0510-4db6-a949-8f74b50f2be9
```

### WordCount MapReduce 예제 실행

1. **java 파일 Driver에 등록**
- src 디렉토리에 새로운 코드를 만들 때 마다 src/Driver.java 파일에 `pgd.addClass`를 새로 넣어야 한다

```java
package ssafy;

import org.apache.hadoop.util.ProgramDriver;

public class Driver {
        public static void main(String[] args) {
                int exitCode = -1;
                ProgramDriver pgd = new ProgramDriver();
                try {
												// src 밑에 있는 클래스 파일 등록
                        pgd.addClass("wordcount", Wordcount.class, "A map/reduce program that performs word counting.");

                        pgd.driver(args);
                        exitCode = 0;
                }
                catch(Throwable e) {
                        e.printStackTrace();
                }

                System.exit(exitCode);
        }
}
```

1. **ant 명령어로 컴파일**
- [Driver.java](http://driver.java) 파일이 바뀌면 **반드시 ant를 다시 수행**해야 한다!

    → src 아래에 있는 모든 java 파일 컴파일하는 명령어 + ssafy.jar라는 패키지를 만들게 됨

```bash
$ cd /home/hadoop/Project
$ ant 
```

- Unix의 make 같은 것으로 Java 개발 환경에서의 표준 빌드 도구
- 자바 파일을 컴파일하는 javac도 있지만, ant는 여러 dependency를 고려하여 소스파일을 컴파일
- src 디렉토리에 있는 것을 다 모아서 컴파일 한 후에 **ssafy.jar** 파일을 생성한다
- Project 디렉토리에 있는 build.xml 파일에 정의한 대로 수행된다

1. **테스트 데이터를 HDFS에 넣기**

```bash
$ cd /home/hadoop/Project
$ hdfs dfs -mkdir wordcount_test // 데이터를 저장할 디렉토리 생성
$ hdfs dfs -put data/workcount-data.txt wordcount_test // 리눅스 파일 시스템에 있는 data 파일을 HDFS에 넣기

**wordcount_test_out 디렉토리가 없어야만 맵 리듀스 코드가 돌아간다!**

$ hdfs dfs -rm -r wordcount_test_out
```

1. **MapReduce 코드 실행**

```bash
$ cd /home/hadoop/Project
$ hadoop jar ssafy.jar wordcount wordcount_test wordcount_test_out

# hadoop jar [jar file] [program name] <input arguments...>
```

1. **결과 확인**

```bash
# reducer 개수를 2개 사용하면 다음과 같은 출력 파일 2개가 생성된다

$ hdfs dfs -cat wordcount_test_out/part-r-00000 | more // 1번 출력 파일을 콘솔에 찍어보기
$ hdfs dfs -cat wordcount_test_out/part-r-00001 | more // 2번 출력 파일을 콘솔에 찍어보기
```

- 각 reducer의 출력 파일은 정렬이 되어있다
- 하지만, reducer들의 출력을 모두 합쳤을 때도 정렬이 되어있는 것은 아니다

### MapReduce 코드에서 사용가능한 Default Class

- 맵 리듀스의 입출력에 사용하는 타입들은 셔플링 페이즈에서 정렬하는데 필요한 비교 함수 등, 여러 함수가 이미 정의되어 있다

    → key-value 형식에서 **key에 대해 정렬**을 할 수 있는 함수와 클래스를 이미 정의해놓은 것!

    - Text: string
    - IntWritable: int
    - LongWritable: long
    - FloatWritable: float
    - DoubleWritable: double
- 만약 새로운 클래스를 정의해서 입출력에 사용하고 싶다면 필요한 여러 함수도 함께 정의를 같이 해주어야 한다 (마치 implement 처럼!)

### [Wordcount.java](http://wordcount.java) 분석

**# TokenizerMapper 함수**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/eaffcfc6-e9f4-4909-81f2-56e06db3e595/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9fab63c8-9f6c-4e49-ad12-0a307191071c/Untitled.png)

- 위의 코드에 따르면 TokenizerMapper 함수의

    입력은 <key: Object, value: Text> 형식의 데이터이고

    출력은 <key: Text, value: IntWritable> 형식의 데이터라는 것을 알 수 있다.

- StringTokenizer에 들어가는 인자는 String 이어야 한다

**# IntSumReducer 함수**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9105ca8c-b2e4-42c4-b3f2-f1aa9d80e75a/Untitled.png)

- reduce 함수에서는 각 결과를 1개의 key값에 모으는 역할을 한다

**# main 함수**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3d84f3a1-0fb4-4754-a682-4814712b94e2/Untitled.png)
