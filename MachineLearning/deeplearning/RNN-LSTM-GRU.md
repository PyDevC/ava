# RNN, LSTM, GRU

Recurrent networks process sequences by carrying a **hidden state** across time steps — the pre-Transformer approach to sequences (text, audio, time series). Each step: combine input `x_t` with the previous hidden state `h_{t-1}`.

```
h_t = f(W·[x_t, h_{t-1}] + b)
```

The same weights `W` are reused at every step — that's "recurrence", and it's what makes RNNs small but hard to train.

## The problem: vanishing/exploding gradients over time

Backprop through time (BPTT) chains the hidden state across *all* time steps, so the gradient is a product of `∂h_t/∂h_{t-1}` terms. Each is < 1 → the past is forgotten (vanishing); > 1 → explosion (see [[backpropagation]]). RNNs effectively can't remember more than ~10 steps, and short-term memory dominates.

## LSTM — the fix with gates

Long Short-Term Memory adds a **cell state** `c_t` (the long-term memory) and three gates, each a sigmoid (see [[../mathematical-function/logistic-function]]) between 0 and 1:

- **forget gate** `f_t`: what to drop from the cell state.
- **input gate** `i_t`: what new info to write.
- **output gate** `o_t`: what to read out to the hidden state.

The key trick: the cell state is updated by *addition* (`c_t = f_t⊙c_{t-1} + i_t⊙ĉ_t`), so the gradient flows through it with a *constant* factor (the forget gate) instead of a matrix product — long-range memory survives.

## GRU — the streamlined LSTM

Gated Recurrent Unit merges forget+input into a single **update gate** and drops the separate cell state. Two gates instead of three, fewer params, ~same performance. The default choice when you want an RNN and LSTM is overkill.

## Where they stand now

For most sequence tasks, **Transformers replaced RNNs** (parallelizable, long-range — see [[Transformers]]). RNNs still appear in:

- **Streaming/online** settings (speech, sensor data) where you can't attend to the future.
- Efficient decoding on tiny devices.
- History: seq2seq with attention (see [[../nlp]] plan) was the bridge RNN→Transformer.

## Related

- [[../mathematical-function/tanh-function]] — the candidate-cell activation.
- [[backpropagation]] — BPTT is the recurring-gradient story.
- [[Transformers]] — the successor architecture.
