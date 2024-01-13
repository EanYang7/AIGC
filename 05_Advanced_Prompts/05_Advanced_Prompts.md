# 05 高级提示工程

提示工程是通过提供更有用的说明或上下文来引导模型做出更相关的响应的过程。

编写提示也有两个步骤，通过提供相关上下文构建提示，第二部分是优化，如何逐步改进提示。

## 提示工程

提示工程是创建将产生所需结果的提示的过程。 提示工程不仅仅是编写文本提示。提示工程不是一门工程学科，它更像是一组可以应用以获得所需结果的技术。

### 一个例子

让我们看一个像这样的基本提示：

> Generate 10 questions on geography.

在这个提示中，实际上应用了一组不同的提示技巧。

让我们来分解一下。

- **上下文**，您指定它应该与“地理”有关。
- **限制输出**，您希望不超过 10 个问题。

### 简单提示的局限性

您可能会也可能不会得到想要的结果。 你会得到你的问题，但地理是一个大话题，你可能无法得到你想要的，原因如下：

- **大话题**，你不知道它是否会涉及国家、首都、河流等等。
- **格式**，如果您希望问题以某种方式格式化怎么办？

正如您所看到的，创建提示时需要考虑很多因素。

到目前为止，我们已经看到了一个简单的提示示例，但生成式人工智能能够为各种角色和行业的人们提供更多帮助。 接下来让我们探讨一些基本技术。

### 提示技巧

首先，我们需要了解提示是 LLM 的一个紧急属性，这意味着这不是模型中内置的功能，而是我们在使用模型时发现的东西。

我们可以使用一些基本技巧来提示 LLM。 让我们来探索一下它们。

- **少样本提示**，这是最基本的提示形式。 这是一个带有几个示例的提示。
- **思维链**，这种类型的提示告诉 LLMs 如何将问题分解为步骤。
- **生成的知识**，为了提高提示的响应，您可以在提示中额外提供生成的事实或知识。
- **从最少到最多**，就像链式分析一样，这种技术是将问题分解为一系列步骤，然后要求按顺序执行这些步骤。
- **自我完善**，这种技术是修正 LLM 的输出，然后要求其改进。
- **多维度提示**。 您在这里想要的是确保 LLM 答案是正确的，并要求它解释答案的各个部分。 这是一种自我完善的形式。

### 少样本Few-shot提示

这种提示风格非常简单，它可能由一个提示和几个示例组成。 当您开始学习 LLMs 时，您可能正在使用这种技术。 这是一个例子：

- Prompt: "What is Algebra?"
- Answer: "Algebra is a branch of mathematics that studies mathematical symbols and the rules for manipulating these symbols."

### 思维链Chain-of-thought

>思路链是向 LLM 展示如何通过提供一系列步骤来解决问题，以及类似的问题以及它们是如何解决的。

思想链是一项非常有趣的技术，因为它涉及到 LLMs 的一系列步骤。 这个想法是以一种让 LLMs 了解如何做某种事情的方式来指导 LLMs。 考虑下面的例子，无论有没有思想链：

```markup
- Prompt: "Alice has 5 apples, throws 3 apples, gives 2 to Bob and Bob gives one back, how many apples does Alice have?"
- Answer: 5
```

LLM 给出的答案为 5，这是不正确的。 根据计算结果 (5 -3 -2 + 1 = 1)，正确答案是 1 个苹果。

那么我们怎样才能教LLM正确地做到这一点呢？

让我们尝试一下思维链。 应用思维链意味着：

1. 给LLM一个类似的例子。
2. 展示计算结果，以及如何正确计算。
3. 提供原始提示。

- Prompt: "Lisa has 7 apples, throws 1 apple, gives 4 apples to Bart and Bart gives one back: 7 -1 = 6 6 -4 = 2 2 +1 = 3
  Alice has 5 apples, throws 3 apples, gives 2 to Bob and Bob gives one back, how many apples does Alice have?" Answer: 1

请注意我们如何用另一个示例、计算和原始提示编写更长的提示，然后得出正确答案 1。

正如您所看到的，思维链是一种非常强大的技术。

### 生成的知识

很多时候，当您想要构建提示时，您希望使用自己公司的数据来实现。 您希望提示的一部分来自公司，另一部分应该是您感兴趣的实际提示。

举个例子，如果您从事保险业务，那么您的提示可能如下所示：

~~~markup
```text
{{company}}: {{company_name}}
{{products}}: 
{{products_list}}
Please suggest an insurance given the following budget and requirements:
Budget: {{budget}}
Requirements: {{requirements}}
```
~~~

在上面，您可以看到如何使用模板构建提示。 模板中有许多变量，用“{{variable}}”表示，这些变量将替换为公司 API 中的实际值。

以下示例展示了变量被公司内容替换后提示的外观：

~~~markup
```text
Insurance company: ACME Insurance
Insurance products (cost per month): 
- Car, cheap, 500 USD
- Car, expensive, 1100 USD 
- Home, cheap, 600 USD
- Home, expensive, 1200 USD
- Life, cheap, 100 USD

Please suggest an insurance given the following budget and requirements:
Budget: $1000
Requirements: Car, Home
```
~~~

通过 LLM 运行此提示将产生如下响应：

~~~markup
```output
, and Life insurance

Given the budget and requirements, we suggest the following insurance package from ACME Insurance: 
- Car, cheap, 500 USD 
- Home, cheap, 600 USD 
- Life, cheap, 100 USD 
Total cost: $1,200 USD
```
~~~

正如您所看到的，它还建议购买人寿保险，但事实并非如此。 此结果表明我们需要通过更改提示来优化提示，使其更清楚地了解其允许的内容。 经过一些试验和错误之后，我们得到以下提示：

~~~markup
```text
Insurance company: ACME Insurance
Insurance products (cost per month): 
- type: Car, cheap, cost: 500 USD
- type: Car, expensive, cost: 1100 USD 
- type: Home, cheap, cost: 600 USD
- type: Home, expensive, cost: 1200 USD
- type: Life, cheap, cost: 100 USD

Please suggest an insurance given the following budget and requirements:
Budget: $1000 restrict choice to types: Car, Home
```
~~~

请注意添加 *type* 和 *cost* 以及使用关键字 *restrict* 如何帮助 LLM 理解我们想要的内容。

现在我们得到以下响应：

~~~markup
```output
Given the budget and requirements, we suggest the Car, Cheap insurance product which costs 500 USD per month.
```
~~~

这个例子的目的是表明，即使我们使用像 _生成知识_ 这样的基本技术，我们仍然需要在大多数情况下优化提示以获得所需的结果。

### 从少到多

从最少到最多提示的想法是将一个更大的问题分解为多个子问题。 这样，您就可以帮助指导 LLMs 如何“克服”更大的问题。 数据科学就是一个很好的例子，你可以要求 LLMs 来划分问题，如下所示：

> Prompt: How to perform data science in 5 steps?

你的人工智能助理会回答：

1. 收集数据
2. 清理数据
3. 分析数据
4. 绘制数据
5. 呈现数据

### 自我完善，质疑结果

对于生成式人工智能和 LLMs，你不能相信其输出。 你需要验证一下。 毕竟， LLMs只是向您展示下一个最有可能说的话，而不是正确的内容。 因此，一个好主意是要求 LLMs 自我批评，这引导我们自我完善技术。

其工作原理是按照以下步骤操作：

1. 要求LLM解决问题的初始提示
2. LLM产生答案
3. 质疑答案并要求人工智能改进
4. LLM 再次回答，这次考虑了质疑并提出了解决方案

您可以根据需要多次重复此过程。

这是使用此技术的示例：

> Prompt: "Create a Python Web API with routes products and customers"

AI Answer:

~~~markup
```python
import flask
app = Flask(__name__)

@app.route('/products')
def products():
    return 'Products'

@app.route('/customers')
def customers():
    return 'Customers'
```
~~~

> Prompt: suggest 3 improvements of the above code

AI Answer:

~~~markup
```text
Sure, here are three suggestions to improve the code:

Import the correct module: The first line should import Flask instead of flask. The correct import statement should be from flask import Flask.

Add a main function: Add a if __name__ == '__main__': block to the end of the file to run the application when the file is executed.

Use f-strings: Use f-strings to format the return statements in the route functions. For example, return 'Products' can be changed to return f'This is the {request.path} page'.
```

```python
from flask import Flask, request

app = Flask(__name__)

@app.route('/products')
def products():
    return f'This is the {request.path} page'

@app.route('/customers')
def customers():
    return f'This is the {request.path} page'

if __name__ == '__main__':
    app.run()
```
~~~

正如您所看到的，由于对第一个答案的批评，上述人工智能响应正在改进第一个建议的代码。

### 助产式Maieutic提示

助产式的提示是一种类似于自我完善的技术，但它更多的是要求 LLMs 解释自己。 目标是减少 LLMs 输出不一致，以确保得出正确的答案。 要遵循的工作流程是：

1. 请 LLM 回答问题
2. 对于答案的每一部分，请LLM更深入地解释。
3. 如果存在不一致，则丢弃不一致的部分。

重复 2 和 3，直到您完成所有部分并对答案感到满意为止。

> prompt: How can I create a crisis plan to mitigate for a pandemic in 5 steps? LLM answer:

~~~markup
```text
1. Identify the risks
1. Identify the stakeholders
1. Identify the resources
1. Identify the actions
1. Identify the communication channels
```
~~~

它确定了 5 个步骤，但是我们可以确定这是否正确吗？ 让我们请LLM解释一下每一步：

> prompt: Explain the first step in more detail, what are the risks in detail with a pandemic?

~~~markup
```text
The risks are the following:

- Loss of life
- Loss of business
- Loss of reputation
- Loss of customers
- Loss of employees
```
~~~

此时，我们希望确保 LLM 是正确的，因此我们要求它更详细地解释第一个风险，并希望它与上面的答案一致：

> prompt: In a Pandemic, which is the biggest risk and why?

~~~markup
```text
The biggest risk is loss of life because it's the most important thing to protect.
```
~~~

> What are the two biggest risks in a pandemic?

~~~markup
```text
The two biggest risks are loss of life and loss of business.
```
~~~

在这一点上，LLMs 是一致的，并提到“生活”和“商业”是两大风险。 我们现在可以继续下一步并感到相当有信心。 然而，我们不应该盲目相信 LLM，我们应该始终验证输出。

## 改变您的输出

LLM 本质上是不确定的，这意味着每次运行相同的提示时都会得到不同的结果。 例如尝试以下提示：

> "Generate code for a Python Web API"

~~~markup
```text
# Import necessary modules
import flask
from flask import request, jsonify

# Create a Flask app
app = flask.Flask(__name__)

# Create a route for the API
@app.route('/api', methods=['GET'])
def api():
    # Get the data from the request
    data = request.args

    # Process the data
    result = process_data(data)

    # Return the result as JSON
    return jsonify(result)

# Function to process the data
def process_data(data):
    # Do something with the data
    result = data + 1

    # Return the result
    return result

# Run the app
if __name__ == '__main__':
    app.run()

```
~~~

再次运行相同的提示会生成略有不同的响应：

~~~markup
```text
#import necessary packages
import flask
from flask import request, jsonify

#create the Flask application
app = flask.Flask(__name__)
app.config["DEBUG"] = True

#create a list of books
books = [
    {'id': 0,
     'title': 'A Fire Upon the Deep',
     'author': 'Vernor Vinge',
     'first_sentence': 'The coldsleep itself was dreamless.',
     'year_published': '1992'},
    {'id': 1,
     'title': 'The Ones Who Walk Away From Omelas',
     'author': 'Ursula K. Le Guin',
     'first_sentence': 'With a clamor of bells that set the swallows soaring, the Festival of Summer came to the city Omelas, bright-towered by the sea.',
     'published': '1973'},
    {'id': 2,
     'title': 'Dhalgren',
     'author': 'Samuel R. Delany',
     'first_sentence': 'to wound the autumnal city.',
     'published': '1975'}
]

#create an endpoint for the API
@app.route('/', methods=['GET'])
def home():
    return '''<h1>Books API</h1>
<p>A prototype API for retrieving books.</p>'''

#create an endpoint to return all books
@app.route('/api/v1/resources/books/all', methods=['GET'])
def api_all():
    return jsonify(books)

#create an endpoint to return a single book
@app.route('/api/v1/resources/books', methods=['GET'])
def api_id():
    #check if an ID was provided as part of the URL
    #if ID is provided, assign it to a variable
    #if no ID is provided, display an error
    if 'id' in request.args:
        id = int(request.args['id'])
    else:
        return "Error: No id field provided. Please specify an id."

    #create an empty list for our results
    results = []

    #loop through the data and match results that fit the requested ID
    #IDs are unique, but other fields might return many results
    for book in books:
        if book['id'] == id:
            results.append(book)

    #use the jsonify function from Flask to convert our list of
    #Python dictionaries to the JSON format
    return jsonify(results)

app.run()
```
~~~

> So is the varied output a problem?

取决于你想做什么。 如果你想要一个具体的回应，那就是一个问题。 如果您可以接受诸如“生成任意 3 个地理问题”之类的不同输出，那么这不是问题。

### 利用温度temperature来改变输出

好的，所以我们决定将输出限制为更可预测，即更具确定性。 我们该怎么做呢？

温度是 0 到 1 之间的值，其中 0 最具确定性，1 最具变化性。 默认值为 0.7。 让我们看看在温度设置为 0.1 的情况下两次运行相同的提示会发生什么：

> "Generate code for a Python Web API"

~~~markup
```python
#import necessary libraries
import flask
from flask import request, jsonify

#create an instance of the Flask class
app = flask.Flask(__name__)

#create an endpoint for the API
@app.route('/api/v1/endpoint', methods=['GET'])
def api_endpoint():
    #get the request data
    data = request.get_json()

    #process the data
    result = process_data(data)

    #return the result
    return jsonify(result)

#function to process the data
def process_data(data):
    #process the data
    result = {'result': 'success'}

    #return the result
    return result

#run the Flask app
if __name__ == '__main__':
    app.run()

```
~~~

再次运行提示符会给我们这样的结果：

~~~markup
```python
#import necessary libraries
import flask
from flask import request, jsonify

#create a Flask app
app = flask.Flask(__name__)

#create a route for the API
@app.route('/api', methods=['GET'])
def api_call():
    #get the data from the request
    data = request.get_json()
    
    #process the data
    result = process_data(data)
    
    #return the result
    return jsonify(result)

#define the process_data function
def process_data(data):
    #do something with the data
    result = data + 1
    
    #return the result
    return result

#run the app
if __name__ == '__main__':
    app.run()

```
~~~

这两个输出之间只有很小的差异。 这次我们反其道而行之，将温度设置为 0.9：

~~~markup
```python
# Import necessary libraries
import flask
from flask import request, jsonify

# Create a Flask app
app = flask.Flask(__name__)

# Create a route for the API
@app.route('/api', methods=['GET'])
def api_call():
    # Get the data from the request
    data = request.args

    # Process the data
    result = process_data(data)

    # Return the result
    return jsonify(result)

# Function to process the data
def process_data(data):
    # Do something with the data
    result = data + 1

    # Return the result
    return result

# Run the app
if __name__ == '__main__':
    app.run()

```
~~~

第二次尝试使用 0.9 作为温度值：

~~~markup
```python
import flask
from flask import request, jsonify

# create the Flask app
app = flask.Flask(__name__)
app.config['DEBUG'] = True

# create some test data
books = [
    {'id': 0, 'title': 'A Fire Upon The Deep', 'author': 'Vernor Vinge', 'first_sentence': 'The coldsleep itself was dreamless.', 'year_published': '1992'},
    {'id': 1, 'title': 'The Ones Who Walk Away From Omelas', 'author': 'Ursula K. Le Guin', 'first_sentence': 'With a clamor of bells that set the swallows soaring, the Festival of Summer came to the city Omelas, bright-towered by the sea.', 'published': '1973'},
    {'id': 2, 'title': 'Dhalgren', 'author': 'Samuel R. Delany', 'first_sentence': 'to wound the autumnal city.', 'published': '1975'}
]

# create an endpoint
@app.route('/', methods=['GET'])
def home():
    return '''<h1>Welcome to our book API!</h1>'''

@app.route('/api/v1/resources/books

```
~~~

正如您所看到的，结果各式各样。

> 请注意，您可以更改更多参数来改变输出，例如 top-k, top-p, repetition penalty, length penalty and diversity penalty ，但这些不属于本课程的范围。

## 补充

以下是一些值得考虑的做法：

- **指定上下文**。 上下文很重要，您可以指定的领域、主题等越多越好。
- **限制输出**。 如果您想要特定数量的项目或特定长度，请指定。
- **指定内容和方式**。 请记住提及您想要什么以及您想要如何实现，例如“创建一个包含路由产品和客户的 Python Web API，将其分为 3 个文件”。
- **使用模板**。 通常，您会希望使用公司的数据来丰富提示。 使用模板来执行此操作。 模板可以包含用实际数据替换的变量。
- **拼写正确**。 LLMs 可能会为您提供正确的答案，但如果您拼写正确，您会得到更好的答案。

