# TensorFlow的求导gradient

tensorflow中有一个计算梯度的函数tf.gradients(ys, xs)用来计算ys关于参数xs的梯度

## xs中的x必须要与ys相关，不相关的话，会报NoneType error

``` python
import numpy  as  np 
import tensorflow as tf  

w1 = tf.get_variable('w1',shape=[3])
w2 = tf.get_variable('w2',shape=[3])
w3 = tf.get_variable('w3',shape=[3])
w4 = tf.get_variable('w4',shape=[3])
z =3*w1+2*w2
z1 = tf.multiply(z,tf.transpose(w3))
z2 = tf.multiply(z,tf.transpose(w4))
grad = tf.gradients([z1],[w1,w2,w3])
with tf.Session() as sess:
    tf.global_variables_initializer().run()
    print(sess.run(grad))
```
**上述代码中如果tf.gradients([z1],[w1,w2,w3,w4]) 就会报错**


## tf.gradients的grad_ys参数
```python
tf.gradients(ys, xs, 
             grad_ys=None, 
             name='gradients',
             colocate_gradients_with_ops=False,
             gate_gradients=False,
             aggregation_method=None,
             stop_gradients=None)
```
求ys对于xs的导数，grad_ys为权重，可以理解为链式求导的相乘。

https://github.com/xitu/tensorflow-docs/wiki/%E6%8E%A8%E8%8D%90%E5%AD%A6%E4%B9%A0%E9%A1%BA%E5%BA%8F




