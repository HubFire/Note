# tensorflow: name_scope 和 variable_scope区别及理解
tensorflow 有name_scope和variable_scope两种方式限定变量的命名空间，variable_scope主要用于变量共享
## tensorflow中创建variable的2种方式

### 1.tf.Variable()：只要使用该函数，一律创建新的variable，如果出现重名，变量名后面会自动加上后缀1，2….
``` python
import tensorflow as tf
with tf.name_scope('cltdevelop):
    var_1 = tf.Variable(initial_value=[0], name='var_1')
    var_2 = tf.Variable(initial_value=[0], name='var_1')
    var_3 = tf.Variable(initial_value=[0], name='var_1')
print(var_1.name)
print(var_2.name)
print(var_3.name)
```

输出如下：
``` python 
cltdevelop/var_1:0
cltdevelop/var_1_1:0
cltdevelop/var_1_2:0
```
### 2.tf.get_variable()：如果变量存在，则使用以前创建的变量，如果不存在，则新创建一个变量。
### TensorFlow的两种tensorflow中的两种作用域
TensorFlow有两种作用域：tf.name_scope和tf.variable_scope
命名域(name scope)：通过tf.name_scope()来实现；
变量域（variable scope）：通过tf.variable_scope()来实现；可以通过设置reuse 标志以及初始化方式来影响域下的变量。 
这两种作用域都会给tf.Variable()创建的变量加上词头，而tf.name_scope对tf.get_variable()创建的变量没有词头影响

``` python
import tensorflow as tf

with tf.name_scope('cltdevelop'):
    var_1 = tf.Variable(initial_value=[0], name='var_1')
    var_2 = tf.get_variable(name='var_2', shape=[1, ])
with tf.variable_scope('aaa'):
    var_3 = tf.Variable(initial_value=[0], name='var_3')
    var_4 = tf.get_variable(name='var_4', shape=[1, ])

print(var_1.name)
print(var_2.name)
print(var_3.name)
print(var_4.name)
```
输出如下：
``` python 
cltdevelop/var_1:0
var_2:0
aaa/var_3:0
aaa/var_4:0
```

## tensorflow的变量共享机制
在tensorflow中变量共享机制是通过tf.get_variable()和tf.variable_scope()两者搭配使用来实现的
``` python  
import tensorflow as tf

with tf.variable_scope('cltdevelop'):
    var_1 = tf.get_variable('var_1', shape=[1, ])
with tf.variable_scope('cltdevelop', reuse=True):
    var_2 = tf.get_variable('var_1', shape=[1,])

print(var_1.name)
print(var_2.name)
``` 
输出如下：
``` python 
cltdevelop/var_1:0
cltdevelop/var_1:0
``` 
***当 reuse 设置为 True 或者 tf.AUTO_REUSE 时，表示这个scope下的变量是重用的或者共享的，也说明这个变量以前就已经创建好了。但如果这个变量以前没有被创建过，则在tf.variable_scope下调用tf.get_variable创建这个变量会报错***

