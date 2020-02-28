# np-transformer

This in an implementation of a single layer of the Transformer module as described in the paper 'Attention is all you need' (Vaswani et al.)[arxiv link][1].

It is a bare-minimum version which I found useful for my purposes.

My main objective was to determine how the self-attention mechanism deals with some toy algorithmic problems and develop intuition on which tasks can or cannot be solved easily.

### Some background
Some time ago I was very interested in memory-augmented architectures and their theoretical power when it comes to solving some simple problems requiring manipulating some external storage (Neural Turing Machine). Similarly to a CPU, we could focus on a given memory location based on its address or its content.
Differentiable Neural Computer (DNC) was another incarnation of this idea, well described in the Nature paper[2]. Here's my toy numpy code which tests DNC on a character-level prediction task.

[https://github.com/krocki/dnc](https://github.com/krocki/dnc)

Those architectures where primarily developed with a hope that they could solve problems in an algorithmic way, just like a computer.

I am very interested in this concept and always try to understand if a given problem can be solved in a machine-learning way of seeing input-output examples. I often perform mental experiments in order to determine the number of registers, memory locations, addressing, etc to have a better understanding of what can be learned.

Let's take a program which copies data.

Pseudocode:

```
while (i<N) {
  mem[dst_base+i] = mem[src_base+i];
  i++;
}
```

A program then for something like 'copy' would ignore the content of memory cells, but instead move something from one location to another while advancing the pointer `[i]`. It means that it should be fairly simple to learn such a program, given that the can focus on a location effectively. One major disadvantage of the `1st generation` of the `algorithm-learning` architectures such as NTM or DNC was the fact that the process was sequential in nature and constrained by the recurrent controller. This simple task was actually not that easy since `i` has to be shifted by some precise disrete amount in every iteration. The process also takes N steps.

One concept which actually worked quite well was the content-based addressing mechanism (key-value dot product). It is quite useful if we need to process data based on some content. 

Let's define a task of filtering an array based on a value (let's say we want only even numbers), then the network needs to learn that the values are important as well as their positions.

```
while (i<N) {
  value = mem[src_base+i];
  mem[dst_base+i] = value is odd ? NULL : value;
  i++;
}
```

We can see that we need to 'query' if the value is odd, which in turn can be learned by a network by forming an odd/even mask and the dot product of that mask and a value will indicate if NULL or the value should be written.

### Transformer

The core of the Tranformer module is the dot-product attention which allows us to focus with respect to some value. Here's the crucial part which makes the Transformer different than the previous approaches - We encode the information about position directly into the input stream and therefore we can process the entire input sequence in parallel instead of sequentially.

Let's go back to the copy example. Since all 'i' memory locations are independent, we can copy the data in a single step, assuming no additional constraints. In a way it's a hack, since we mark every item with a tag and later we effectively use content-based addressing to determine the location. It works well in practice.

Here are some experiments I tried and they give some insight into how things are learned.

This version is NOT optimized for performance.

### Usage:
```
  python3 transformer.py -t {copy, reverse, rotate, filter} [-l saved_model] [-S sequence length]
```

Example:
```
  python3 transformer.py -t reverse
```

[1]: https://arxiv.org/pdf/1706.03762.pdf
