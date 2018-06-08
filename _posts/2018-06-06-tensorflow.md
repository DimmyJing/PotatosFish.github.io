This is a (semi)in-depth overview of machine learning using python bindings for tensorflow.

Everything is written as I learn, so some parts might be off.

This post would be continuously updated.

Having basic knowledge of machine learning is recommended, since I wouldn't be able to cover everything.

I would also use lower level bindings to train my models, but just keep in mind that there are already higher level predictors that tensorflow provides.

# Tensorflow

Programming in tensorflow is different than programming normal applications, making machine learning models faster to write

## Sessions

The most unique part of programming in tensorflow is sessions. It allows users to easily train new models while simplifying their code.

In tensorflow, most operations does not actually compute at the time the command was parsed. Instead, they would run in sessions.

```python
import tensorflow as tf
str = tf.constant("Hello")

print(str)
# Tensor("Const:0", shape=(), dtype=string)

with tf.Session() as sess:
	print(sess.run(str))
	# b'Hello'
```

As seen here, str is viewed as a tensor outside of the session, but was viewed as a string in the session.

```python
import tensorflow as tf
a = tf.Variable(2)
b = a * 3
init = tf.global_variables_initializer()

print(b)
# Tensor("mul:0", shape=(), dtype=int32)

with tf.Session() as sess:
	sess.run(init)
	print(sess.run(b))
	# 6
```

As seen here, b is only calculated in the session, where it is seen as a multiply tensor outside of the session.

tf.global\_variables\_initializer() is there to initialize the variables. It is required everytime the session is introduced.

The benefits of session would be explained later, when placeholders and feed dictionarys are introduced.

## Models

Normally when programming in tensorflow, you would make a model and train it until its accuracy is acceptable.

The model is a combination of operations, with variables and special algorithms.

After the model is constructed, it is essentially a black box that takes in labels and spits out predicted values.

The modification of variables in the model is automatically done my the optimizers that you choose, making it easier to get a basic machine learning model working.

In tensorflow, one thing that you would do constantly is to edit the parameters passed into the functions. Those are called hyper parameters.

Different settings require different parameters, and the exact value mostly comes from experience.

# Basic Tensorflow: Finding line of best fit

Neural networks can be viewed as a way of getting the line of best fit, but with multiple dimensions.

For now, I am only going to construct a model that predicts linear equations.

```python
import tensorflow as tf
import numpy as np

def get_batch(amount):
	x = np.random.randint(100, size=[amount])
	y = [i * 5 + 8 for i in x]
	# wx + b
	# 5x + 8
	return x, y

x = tf.placeholder(tf.float32, shape=[None])
y_true = tf.placeholder(tf.float32, shape=[None])
W = tf.Variable(0.)
b = tf.Variable(0.)
y = x * W + b
optimizer = tf.train.AdamOptimizer(learning_rate = 0.01)
train = optimizer.minimize(tf.square(y_true - y))
init = tf.global_variables_initializer()

with tf.Session() as sess:
	sess.run(init)
	for step in range(10000):
		x_batch, y_batch = get_batch(100)
		sess.run(train, feed_dict={x:x_batch, y_true:y_batch})
	print(sess.run(W))
	print(sess.run(b))
```

After the code runs, it should output something similar to 5.0001373 and 7.9909186, which is very close to the actual values, 5 and 8.

I will explain the code line by line in the following section.

## Explanation

First of all, we need to import the libraries that we're going to use.

```python
import tensorflow as tf
import numpy as np
```

Then we need to create a function that is able to get a batch of samples for training.

```python
def get_batch(amount):
	x = np.random.randint(100, size=[amount])
	# makes an array of size amount with integer values [0,100)

	y = [i * 5 + 8 for i in x]
	# makes an array of size amount that is the result of the linear equation 5x + 8

	return x, y
```

We need some placeholder values to train the dataset with.

```python
x = tf.placeholder(tf.float32, shape=[None])
# the type of x is float32, and it is a 1 dimensional array of undetermined size. The 'None' makes it able to accept any number of data

y_true = tf.placeholder(tf.float32, shape=[None])
# this is the true result for the linear equation, and it would be used in the optimizer to calculate the loss between the predicted value and the actual result
```

We need variables that would be modified to refine the model

```python
W = tf.Varibale(0.)
# This is the weight, normally a multiplier. The 0. is to set it as a float.

b = tf.Variable(0.)
# This is the bias, normally a adder.
```

Then we need to calculate the predicted value using the variables

```python
y = x * W + b
# Most operators in python is supported by tensorflow, so instead of doing tf.multiply(x, W), you could do x * W
```

Then we need to create the optimization function, the one that would be used for training

```python
optimizer = tf.train.AdamOptimizer(learning_rate=0.01)
# There are two major optimizers, gradient descent and Adam.
# Adam optimizer normally completes the process in less steps and have less reliance on learning rate, but it is slower than gradient descent.
# The learning rate is the rate of which the variable normally changes. The lower the longer it takes to reach the desired outcome, but having it too high would cause it to overshoot.

train = optimizer.minimize(tf.square(y_true - y))
# This is the function that would be used for training.
# The reason for the square is so that larger differences would be much larger and smaller difference would be much smaller, causing the optimizer to fix the large problems, then the small ones.
```

We also need to have a function that initializes the global variables

```python
init = tf.global_variables_initializer()
```

Finally we need to run the model

```python
with tf.Session() as sess:
	sess.run(init)
	# initialize the global variables

	for step in range(10000):
		x_batch, y_batch = get_batch(100)
		# call the previously written get_batch function to get 100 x and its corresponding y values

		sess.run(train, feed_dict={x:x_batch, y_true:y_batch})
		# run the optimizer minimize function.
		# the feed_dict is where you put data into the model. x_batch would be put into x, y_batch would be put into y_true, and the optimizer would change the value according to the dataset.

	# print the weight and the bias
	print(sess.run(W))
	print(sess.run(b))
```

Neural networks cannot be perfect, but the final answer is close enough in most cases.

This is the simplest neural network with only only neuron and label, but the code is the basis for many dnn networks.
