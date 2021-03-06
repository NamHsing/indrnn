# Independently Recurrent Neural Networks

Simple TensorFlow implementation of [Independently Recurrent Neural Network (IndRNN): Building A Longer and Deeper RNN](https://arxiv.org/pdf/1803.04831.pdf) by Shuai Li et al. The author's original implementation in Theano and Lasagne can be found in [Sunnydreamrain/IndRNN_Theano_Lasagne](https://github.com/Sunnydreamrain/IndRNN_Theano_Lasagne).

## Summary

In IndRNNs, neurons in recurrent layers are independent from each other. The basic RNN calculates the hidden state `h` with `h = act(W * input + U * state + b)`. IndRNNs use an element-wise vector multiplication `u * state` meaning each neuron has a single recurrent weight connected to its last hidden state. 

The IndRNN 
- can be used efficiently with ReLU activation functions making it easier to stack multiple recurrent layers without saturating gradients
- allows for better interpretability, as neurons in the same layer are independent from each other
- prevents vanishing and exploding gradients by regulating each neuron's recurrent weight

## Usage

Copy [ind_rnn_cell.py](https://github.com/batzner/indrnn/blob/master/ind_rnn_cell.py) into your project.

```python
from ind_rnn_cell import IndRNNCell

# Regulate each neuron's recurrent weight as recommended in the paper
recurrent_max = pow(2, 1 / TIME_STEPS)

cell = MultiRNNCell([IndRNNCell(128, recurrent_max_abs=recurrent_max),
                     IndRNNCell(128, recurrent_max_abs=recurrent_max)])
output, state = tf.nn.dynamic_rnn(cell, input_data, dtype=tf.float32)
...
```
## Experiments in the paper

### Addition Problem

See [examples/addition_rnn.py](https://github.com/batzner/indrnn/blob/master/examples/addition_rnn.py) for a script reproducing the "Adding Problem" from the paper. Below are the results reproduced with the `addition_rnn.py` code.

![https://github.com/batzner/indrnn/raw/master/img/addition/TAll.png](https://github.com/batzner/indrnn/raw/master/img/addition/TAll.png)
 
### Sequential MNIST

See [examples/sequential_mnist.py](https://github.com/batzner/indrnn/blob/master/examples/sequential_mnist.py) for a script reproducing the Sequential MNIST experiment. I let it run for two days and stopped it after 60,000 training steps with a

- Training error rate of 0.7%
- Validation error rate of 1.1%
- **Test error rate of 1.1%**

![https://github.com/batzner/indrnn/raw/master/img/sequential_mnist/errors.png](https://github.com/batzner/indrnn/raw/master/img/sequential_mnist/errors.png)


## Requirements
- Python 3.4+
- TensorFlow 1.5+
