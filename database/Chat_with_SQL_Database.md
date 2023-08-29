<a href="https://colab.research.google.com/github/sugarforever/LangChain-SQL-Chain/blob/main/Chat_with_SQL_Database.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>


```python
%pip install langchain
%pip install pymysql
```

    Looking in indexes: https://pypi.tuna.tsinghua.edu.cn/simpleNote: you may need to restart the kernel to use updated packages.
    
    Requirement already satisfied: langchain in d:\users\mark2\miniconda3\lib\site-packages (0.0.147)
    Requirement already satisfied: SQLAlchemy<2,>=1 in d:\users\mark2\miniconda3\lib\site-packages (from langchain) (1.4.47)
    Requirement already satisfied: dataclasses-json<0.6.0,>=0.5.7 in d:\users\mark2\miniconda3\lib\site-packages (from langchain) (0.5.7)
    Requirement already satisfied: PyYAML>=5.4.1 in d:\users\mark2\miniconda3\lib\site-packages (from langchain) (6.0)
    Requirement already satisfied: numexpr<3.0.0,>=2.8.4 in d:\users\mark2\miniconda3\lib\site-packages (from langchain) (2.8.4)
    Requirement already satisfied: pydantic<2,>=1 in d:\users\mark2\miniconda3\lib\site-packages (from langchain) (1.10.7)
    Requirement already satisfied: tqdm>=4.48.0 in d:\users\mark2\miniconda3\lib\site-packages (from langchain) (4.64.1)
    Requirement already satisfied: numpy<2,>=1 in d:\users\mark2\miniconda3\lib\site-packages (from langchain) (1.22.3)
    Requirement already satisfied: openapi-schema-pydantic<2.0,>=1.2 in d:\users\mark2\miniconda3\lib\site-packages (from langchain) (1.2.4)
    Requirement already satisfied: aiohttp<4.0.0,>=3.8.3 in d:\users\mark2\miniconda3\lib\site-packages (from langchain) (3.8.4)
    Requirement already satisfied: requests<3,>=2 in d:\users\mark2\miniconda3\lib\site-packages (from langchain) (2.28.1)
    Requirement already satisfied: async-timeout<5.0.0,>=4.0.0 in d:\users\mark2\miniconda3\lib\site-packages (from langchain) (4.0.2)
    Requirement already satisfied: tenacity<9.0.0,>=8.1.0 in d:\users\mark2\miniconda3\lib\site-packages (from langchain) (8.2.2)
    Requirement already satisfied: yarl<2.0,>=1.0 in d:\users\mark2\miniconda3\lib\site-packages (from aiohttp<4.0.0,>=3.8.3->langchain) (1.7.2)
    Requirement already satisfied: aiosignal>=1.1.2 in d:\users\mark2\miniconda3\lib\site-packages (from aiohttp<4.0.0,>=3.8.3->langchain) (1.3.1)
    Requirement already satisfied: charset-normalizer<4.0,>=2.0 in d:\users\mark2\miniconda3\lib\site-packages (from aiohttp<4.0.0,>=3.8.3->langchain) (2.0.4)
    Requirement already satisfied: multidict<7.0,>=4.5 in d:\users\mark2\miniconda3\lib\site-packages (from aiohttp<4.0.0,>=3.8.3->langchain) (6.0.2)
    Requirement already satisfied: frozenlist>=1.1.1 in d:\users\mark2\miniconda3\lib\site-packages (from aiohttp<4.0.0,>=3.8.3->langchain) (1.3.3)
    Requirement already satisfied: attrs>=17.3.0 in d:\users\mark2\miniconda3\lib\site-packages (from aiohttp<4.0.0,>=3.8.3->langchain) (22.2.0)
    Requirement already satisfied: marshmallow-enum<2.0.0,>=1.5.1 in d:\users\mark2\miniconda3\lib\site-packages (from dataclasses-json<0.6.0,>=0.5.7->langchain) (1.5.1)
    Requirement already satisfied: marshmallow<4.0.0,>=3.3.0 in d:\users\mark2\miniconda3\lib\site-packages (from dataclasses-json<0.6.0,>=0.5.7->langchain) (3.19.0)
    Requirement already satisfied: typing-inspect>=0.4.0 in d:\users\mark2\miniconda3\lib\site-packages (from dataclasses-json<0.6.0,>=0.5.7->langchain) (0.8.0)
    Requirement already satisfied: typing-extensions>=4.2.0 in d:\users\mark2\miniconda3\lib\site-packages (from pydantic<2,>=1->langchain) (4.5.0)
    Requirement already satisfied: certifi>=2017.4.17 in d:\users\mark2\miniconda3\lib\site-packages (from requests<3,>=2->langchain) (2022.12.7)
    Requirement already satisfied: idna<4,>=2.5 in d:\users\mark2\miniconda3\lib\site-packages (from requests<3,>=2->langchain) (3.4)
    Requirement already satisfied: urllib3<1.27,>=1.21.1 in d:\users\mark2\miniconda3\lib\site-packages (from requests<3,>=2->langchain) (1.26.14)
    Requirement already satisfied: greenlet!=0.4.17 in d:\users\mark2\miniconda3\lib\site-packages (from SQLAlchemy<2,>=1->langchain) (2.0.2)
    Requirement already satisfied: colorama in d:\users\mark2\miniconda3\lib\site-packages (from tqdm>=4.48.0->langchain) (0.4.6)
    Requirement already satisfied: packaging>=17.0 in d:\users\mark2\miniconda3\lib\site-packages (from marshmallow<4.0.0,>=3.3.0->dataclasses-json<0.6.0,>=0.5.7->langchain) (23.1)
    Requirement already satisfied: mypy-extensions>=0.3.0 in d:\users\mark2\miniconda3\lib\site-packages (from typing-inspect>=0.4.0->dataclasses-json<0.6.0,>=0.5.7->langchain) (1.0.0)
    Looking in indexes: https://pypi.tuna.tsinghua.edu.cn/simple
    Requirement already satisfied: pymysql in d:\users\mark2\miniconda3\lib\site-packages (1.0.3)
    Note: you may need to restart the kernel to use updated packages.
    


```python
%env OPENAI_API_KEY=sk-d8X5qdQlAnTdOHzCiOdZT3BlbkFJ
```

    env: OPENAI_API_KEY=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
    


```python
from langchain import OpenAI, SQLDatabase, SQLDatabaseChain
```


```python
db = SQLDatabase.from_uri("mysql+pymysql://root:root@127.0.0.1/students")
llm = OpenAI(temperature=0)
```


```python
db_chain = SQLDatabaseChain(llm=llm, database=db, verbose=True)
```


```python
db_chain.run("全班有多少个学生?")
```

    
    
    [1m> Entering new SQLDatabaseChain chain...[0m
    全班有多少个学生?
    SQLQuery:[32;1m[1;3m SELECT COUNT(*) FROM students;[0m
    SQLResult: [33;1m[1;3m[(5,)][0m
    Answer:[32;1m[1;3m 全班有5个学生。[0m
    [1m> Finished chain.[0m
    




    ' 全班有5个学生。'




```python
db_chain.run("他们的名字是什么?")
```

    
    
    [1m> Entering new SQLDatabaseChain chain...[0m
    他们的名字是什么?
    SQLQuery:[32;1m[1;3m SELECT student_name FROM parents[0m
    SQLResult: [33;1m[1;3m[('Alex',), ('Alice',), ('Jack',), ('Ophelia',)][0m
    Answer:[32;1m[1;3m Alex, Alice, Jack, Ophelia.[0m
    [1m> Finished chain.[0m
    




    ' Alex, Alice, Jack, Ophelia.'




```python
db_chain.run("他们的平均分是多少?")
```

    
    
    [1m> Entering new SQLDatabaseChain chain...[0m
    他们的平均分是多少?
    SQLQuery:[32;1m[1;3m SELECT AVG(score) FROM students;[0m
    SQLResult: [33;1m[1;3m[(Decimal('61.0000'),)][0m
    Answer:[32;1m[1;3m 他们的平均分是61分。[0m
    [1m> Finished chain.[0m
    




    ' 他们的平均分是61分。'




```python
db_chain.run("去除0分之后的平均分是多少")
```

    
    
    [1m> Entering new SQLDatabaseChain chain...[0m
    去除0分之后的平均分是多少
    SQLQuery:[32;1m[1;3m SELECT AVG(score) FROM students WHERE score > 0;[0m
    SQLResult: [33;1m[1;3m[(Decimal('76.2500'),)][0m
    Answer:[32;1m[1;3m 去除0分之后的平均分是76.25[0m
    [1m> Finished chain.[0m
    




    ' 去除0分之后的平均分是76.25'




```python
db_chain.run("谁的了0分?")
```

    
    
    [1m> Entering new SQLDatabaseChain chain...[0m
    谁的了0分?
    SQLQuery:[32;1m[1;3m SELECT `name` FROM `students` WHERE `score` = 0;[0m
    SQLResult: [33;1m[1;3m[('Ophelia',)][0m
    Answer:[32;1m[1;3m Ophelia的了0分。[0m
    [1m> Finished chain.[0m
    




    ' Ophelia的了0分。'




```python
db_chain.run("谁的了0分以及为什么")
```

    
    
    [1m> Entering new SQLDatabaseChain chain...[0m
    谁的了0分以及为什么
    SQLQuery:[32;1m[1;3m SELECT `name`, `score`, `teacher_note` FROM `students` WHERE `score` = 0 LIMIT 5;[0m
    SQLResult: [33;1m[1;3m[('Ophelia', 0, 'Unfortunately, Ophelia missed the test.')][0m
    Answer:[32;1m[1;3m Ophelia了0分，因为不幸的是，Ophelia错过了考试。[0m
    [1m> Finished chain.[0m
    




    ' Ophelia了0分，因为不幸的是，Ophelia错过了考试。'




```python
db_chain.run("说得了0分，给出父母的电话")
```

    
    
    [1m> Entering new SQLDatabaseChain chain...[0m
    说得了0分，给出父母的电话
    SQLQuery:[32;1m[1;3m SELECT parent_name, parent_mobile FROM parents WHERE student_name = 'Alex' LIMIT 5;[0m
    SQLResult: [33;1m[1;3m[('Barry', '0881234567')][0m
    Answer:[32;1m[1;3m Alex的父母的电话是0881234567。[0m
    [1m> Finished chain.[0m
    




    ' Alex的父母的电话是0881234567。'


