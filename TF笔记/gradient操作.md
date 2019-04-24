# TensorFlow的求导gradient

tensorflow中有一个计算梯度的函数tf.gradients(ys, xs)用来计算ys关于参数xs的梯度

## xs中的x必须要与ys相关，不相关的话，会报NoneType error

``` python
import numpy  as  np 
import tensorflow as tf  
with tf.name_scope("share"):
    w1 = tf.get_variable('w1',shape=[3])
    w2 = tf.get_variable('w2',shape=[3])
    with tf.name_scope("w3"):
        w3 = tf.get_variable('w3',shape=[3])
    with tf.name_scope("w4"):
        w4 = tf.get_variable('w4',shape=[3])
z =3*w1+2*w2
z1 = tf.multiply(z,tf.transpose(w3))
z2 = tf.multiply(z,tf.transpose(w4))
grad = tf.gradients([z1],[w1,w2,w3])
with tf.Session() as sess:
    tf.global_variables_initializer().run()
    print(sess.run(grad))
```
** 上述代码中如果tf.gradients([z1],[w1,w2,w3,w4]) 就会报错 **




