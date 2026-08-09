# DL Compilers

Deep Learning compilers are domain specific compilers that are designed to optimize deep learning models to run faster on specific hardware. Deep Learning compiler technology tries to map the operations used to represent deep learning models to specific computation units (depending upon the target language), these computation units can be fused together or auto-tuned for allowing better code generation by exploring the optimal possible set of configurations used for generating computation units.

## Explain me above definition

Deep Learning compilers are specialized compilers that can read the model and produce code like assembly, binary, or GPU code so we can run them without much overhead. Now since we have to do all the data movements and need to reduce the redundant operations, and support various features offered by your hardware, we develop several optimizations that can help us produce better code which will decrease our execution time.

When it's said we map the operations to computation units, I mean that operations like LayerNorm, ReLU, Conv2D, are translated as simpler forms like representing operations like add -> mul -> div -> sum => simple model representation. Ofcourse the process is far complex and requires us to exploit various features that allows us to optimize our model representations that we produced.

> Rest of the things I mentioned as keywords that should be studied by the individual.

## The Idea to represent models

In most cases the initial representation of model is considered to be graphs, we can represent data flow, computation structure, in the form of inter connected nodes. Representing our model as graph gives us initial stages of optimizations such as graph simpilification, dead code elimination, common sub expression elimination, and helps us walk through the graph and generate lower-levels of representations easily.
