# DL Compilers

Deep Learning compilers are domain specific compilers that are designed to optimize deep learning models to run faster on specific hardware. Deep Learning compiler technology tries to map the operations used to represent deep learning models to specific computation units (depending upon the target language), these computation units can be fused together or auto-tuned for allowing better code generation by exploring the optimal possible set of configurations used for generating computation units.

## Explain me above definition

Deep Learning compilers are specialized compilers that can read the model and produce code like assembly, binary, or GPU code so we can run them without much overhead. Now since we have to do all the data movements and need to reduce the redundant operations, and support various features offered by your hardware, we develop several optimizations that can help us produce better code which will decrease our execution time.

When it's said we map the operations to computation units, I mean that operations like LayerNorm, ReLU, Conv2D, are translated as simpler forms like representing operations like add -> mul -> div -> sum => simple model representation. Ofcourse the process is far complex and requires us to exploit various features that allows us to optimize our model representations that we produced.

> Rest of the things I mentioned as keywords that should be studied by the individual.

## The Idea to represent models

In most cases the initial representation of model is considered to be graphs, we can represent data flow, computation structure, in the form of inter connected nodes. Representing our model as graph gives us initial stages of optimizations such as graph simpilification, dead code elimination, common sub expression elimination, and helps us walk through the graph and generate lower-levels of representations easily.

## Supported DL Optimizations

- Quantization -> Converting High precision bits of a model to low-bit to decrease the memory usage, but it comes at a cost of accuray reduction.

- Dynamic Shapes -> Supporting compilation for variable sequence length of input tensor.

- Auto-tuning -> Searching through an optimal set of mapping options such as tiling, block size, etc. using either ML Algo or heuristics, as well as genetic search.

## Architectural Design of DL Compilers

## Design Overview

Three component to DL Compiler:
- Front end
- Back end
- IR (present in both frontend as well as backend and also in between for bridging the two)

Two kinds of IRs:
- High Level IR (model agnostic)
- Low Level IR (hardware agnostic)

## High Level IR
High Level IR is general represented as Graph IR which shows computation, data flow, control flow and is hardware independent. It allows us to express diverse set of model and provide a better interface to apply graph level optimizations as it contains a rich set of semantic information about the operation as well as model as whole.

## Low Level IR
Low Level IR is hardware centric IR, it is represents the operations in optimized model in a set of instructions which can be ported to most hardware and could get plugged to different compiler backend tool chains such as Halide, Polyhedral models, LLVM.

## FrontEnd of DL Compilers

The Frontend is an interface for a DL Framework, it captures the computation graph to transform it later into graph IR and apply various transformations and optimizations techniques.

Optimization techniques are classified into:
- node-level: nop elimination, zero-dim-tensor elimination
- block-level: algebraic simpilification, operator fusion, operator sinking
- dataflow-level: CSE, DCE, static memory planning and layout transformation.

After all the optimizations are applied then it's passed to the backend to codegen or, in some compilers to generate Opeartor IR to further optimize or doing progressive lowering.

## BackEnd of DL Compilers
The BackEnd transforms the high level IR to Low level IR and performs hardware specific optimizations in the process. After Low Level IR generation it code generate for CPU or GPU.

Two ways backend can codegen:
- Transfer to Generic Compiler Tool kits to apply general purpose optimizations for device code generation.
- Take in the Low-level IR and high level IR analysis to apply more efficient code generation.

Hareware specific optimizations include:
- Hardware intrinsic mapping
- memory allocation and fetching
- memory latency hiding
- parallelization
- loop oriented optimization.

To address the large solution space introduced by possibility of code generation and optimizations, two approaches are adopted such as auto-scheduling (polyhedral model) and auto-tuning (AutoTVM, Triton Autotune).

## High Level IR
### Semantic of Graph IR

The Below is the demonstrate how DL Compilers analyze Graph IR code.

There are two main graph IR representation:
- DAG-based IR: DAG based IR is used to represent computation graphs where it's nodes and edges are organized as directed acyclic graph. 
    There are several DAG Level Optimizations available:
    - Live variable analysis
    - variable dependency analysis
    It is good but it has one deficiency which is semantic ambiguity cause by the undefinition of computation scope.

- Bind-based IR: Let-binding is IR to restrict the scope of a computation by adding Let expression to a function, this solves the problem of semantic ambiguity since there are scope restrictions. When a computaiton needs to return a result it in DAG based IR, it first needs to resolve the node recursively which is not a problem in Bind-based IR since it can be managed using variable map for Let expressions.

#### Computation Representation
Since there are several IR implementation for different DL frameworks, the compilers prefer to create their own specific representation forms which allows them to support different frameworks.

The Representations forms are classified into three forms:
- Function based form: In this form the operators are encapsulated in framework specific operators. Due to this it's hard to retrive itermediate values used in the computation. HLO uses this form.
    It has three components:
    - HloModule: represents the whole program
    - HloComputation: represents the function
    - HloInstruction: represents the instruction in function

- Lambda expression: Here the computation is represented as lambda expressions (same as one in python or C++), TVM uses it. The computations are represented as combination of output tensor representation and lambda expression to state the computation rule.

- Einstien notation: (Since it's difficult to understand I will only give example), in this notatation, we define the computation as summation functions where the computation is applied as rule of summations. (Read the page 12 of Deep Learning Compiler survey).

### Graph Representation
#### Data Organization

In DL Compilers data is represented with several levels of heirarchy, where each performs progresses to perform whole computation of the model. The single model is first represented as sub graphs, where each sub graph forms a DAG. Each node in sub graph or stage graph has a single operator node, which can be either Compute node, TensorCompute node, etc. The nodes are arranged according to the Scheduler which decides arranges them in order to get higher performance.

#### Bound Inference

The computation graph or model does not depict the genearl upper bound or lower bounds for iterations performed in the computation, hence we are required to perform data analysis of bounds using information we get from input and outputs of the model.

There are two ways you can calculate bounds inference:

- Bottom up: From output to input node.
- Top down: From input to output node.

### Data Representation

DL Compilers need to represent the data such as inputs, weights, outputs, variables, etc. To specify this they use either pointer to the tensor or data location or using a light weight approach, Placeholder. Placeholder is used to represent a data body with no information about the tensor, this allows the optimizer to change the shapes of the Placeholder instead of the whole semantics.

When dealing with Dynamic shapes, the Placeholders do not have the shape information and thus are required to perform some shape calculations as runtime. During compilation it is required by the optimizer to take into account the variable length of input to guarantee the correctness of the computation and has memory validity.

Compilers uses various forms of Data Layouts such as operator view, backend view, and tensor view. 

### Operator support
The computational operators provided by DL Compilers can be divided into three levels:

- basic calculation nodes: These are computational nodes example: exponent arithmetics.
- high-level calculation nodes: high-level nodes are nodes with direct linkage to neural network nodes such as Convolution node, polling etc.
- fuse calculation nodes: These are the nodes that are fused together by the graph optimizations.

Some compilers support extension for custom operators for things like new backend instruciton, new neural network layer etc. 

To add custom operators in the existing IR we can be done at two levels:

- Node level: Here the nodes are expected to be extended as block of already existing IR nodes. Here we reuse all the functionality of existing IR and avoid implementing new operator from scratch. Adding operators this way requires significant additions to the IR layout and we are expected to encapsulate the node to act as a proper operator. To add custom operators in Glow we need to do node registeration, layout specification, opeartor load completing, lowering steps, opeartion IR generation, and instruction generation.

- Operator level: This is relatively easier form of extensiblity since the user only needs to add external functions as operators, frameworks like TVM treats external functions as black boxes and supports custom operators without perform various complex steps to add opeartor.


There are various kinds of operators used in the DL Compilers:

- Broadcasting: Broadcasting allows for different shapes of tensors to get accepted by an operator without imposing strict shape policy. Such as allowing addition between tensor of completely different shapes, these are required in performing operations such as element-wise addition between matrix and vector.

- Predication: This operator brings the asserts in to the mainstream to decide which code to execute and which to not, this can be exploited at optimization step to decide if the code will run or not. Some compilers do not provide support for predication.

- Automatical differentiation: This operator provides the ability of the compiler to perform differentiation on operators.

- Control Flow: Control flow allows for complex representation of the computational graph by adding conditionals to the graph.

## Low-level IR
Low-level IR describe the computations in a more fine-grain representation, this enables target dependent optimizations by providing interface to tune the computation and memory access. It allows developers to make use of various third party tool chains such as Halide, Polyhedral model etc.

There are three category of Low level IR:

- Halide based IR: This IR separate the computation from scheduling, rather than giving a specific scheme it decided to search through possible scheduling and choose the best one. Halide based IR are given some improvements by adding the support for finite shapes and various other features.

- Polyhedral based IR: The polyhedral model uses linear programmig and other mathematical methods to optimize loop-based codes where the control flow of bound and branch is static. Polyhedral based IR undergoes multiple polyhedral transformations such as fusion, tiling, sinking, mapping, etc. including both device-dependent and device-independent optimization. When targeting a specific architecture.

- Other unique IR: Some DL Compilers create their own IR and perform hardware specific optimizations and lower the IR directly to LLVM IR.

Low level IR are able to exploit various hardware specific instructions and optimizations and allow the compiler to create a schedule to hide the latency of memory operations. There are two sections of instructions-based functions in low-level IR: declare and program. The first section gives the number of constant memory regions that live throughout the lifetime of the program and second section gives the locally allocated regions, including functions and temp variables.

Some Compilers add qualifiers to the operands which tells the compiler from where to access the memory, allowing for further memory optimizations.

### Code Generation based on Low-level IR
Eventually most of the DL Compilers are lowered to LLVM IR utilizing mature optimization and diverse hardware code generation from the infrastructure.

LLVM IR can generate poor code if low-level IR is passed directly to LLVM IR. To avoid this and utilize the hardware dependent optimizations compilers:

1. Perform target-specific loop transformation in the upper IR of LLVM.
2. provide additional information about the hardware targe t for the optimization pass.

The compilation can be done in two ways:
- Just in Time (JIT)
- Ahead of time (AOT)

In just-in-time the target code is generated on fly and can optimize code with better runtime knowledge. AOT is another approach for DL Compilers, which generates all the binaries first and execute them after wards. AOT allows the use of static analysis than JIT.

Depending on these common devices the DL Compilers generate the code in this format:

- CPU: LLVM IR and then code gen based on the specific CPU architecture such as X86 and ARM.

- GPU: Here we basically generate GPU assembly instructions such as NVIDIA PTX using LLVM NVPTX and in AMD there are various instructions sets to lower it to.


## Frontend Optimizations
After constructing the computation graph the frontend applies graph level optimizations.

These optimizations are hardware independent. In DL compilers the graph nodes are coarse operations where each node represents the opeartions on tensor and and edge represents the dependty between two opeartions. Compilers provide a pass infrastructure to apply optimizations to the node blocks (list of nodes that are dependent enough to act as a single node). Passes traverses the whole graph to apply the optimization by pattern matching.

Graph transformations are done in two steps:
- capture specific feature from computation graph
- rewrite teh parts of sub graph for optimization.

There are several frontend optimizations available:

- node-level optimizations
- block-level optimizations
- dataflow-level optimizations

### Node level optimizations
Nodes in Computation graph has the knowledge of input tensor and output tensor of every operation which helps in transformation of the graph in two ways: Node elimination, node replacement.

In Node elimination we remove the nodes that does not have semantic affect on the model's output, and node replacement is used to replace the costly operations with lighter, low cost operations.

Ex:

Nop elimination: it remove the operations that are lacking adequate inputs, like sum operation  with only one input tensor, or padding operation with zero padding etc.

Zero-dim-tensor elimination: it removes the unecessary operations whose inputs are zero-dim tensors. It also includes 0s in the shapes of a high dim tensor like {0, 2, 3}, the optimizations can be applied only when it does not affect the correctness of the model.

### Block level optimizations
These operations are applied on the blocks of nodes rather than individual nodes, it requires analysis for grouping different nodes together to exploit optimization.

#### Algebraic Simpilification (AS)
The AS optimizations can be performed in three steps:

- Algebraic identities: change the computation of simple operation like x * 1 -> x.
- Strength reduction: replace the expensive opeartions with cheaper alternatives x * 2 -> x << 1
- constant folding: resolving the constants at compile time rather than at runtime.

For DL specific operations such reshape, transpose, polling, etc. the optimziation can involve reordering the opeartions and sometime eliminating them completely, which reduces the redundancy and improves the efficiency.

#### Operator Fusion

Operator fusion are optimziations that allows us to reduce the memory read and writes and thus remove the memory bottlenecks from the model. It enables better sharing of computation, removal of intermedicate allocations, facilitates further optimization by combining loop nests, as well as reduces lauch and synchronization overhead.

The opeartors are classified into four categories:

- injective
- reduction
- complex-out-fusible
- opaque

There are several optimizations possible for particular category of opeartor:

- mutiple injective operators can be fused into a new injective operator
- a reduction operator can be fused with input injective operators before input
- complex-out-opeartors can be fused with injective opeartors after the output according to the operator and fusion rules

Note that the opaque operators can't be fused at all.

#### Operator Sinking
This optimization sinks the operations such as transposes below opeartions like a batch norm, ReLU, sigmoid, channel shuffle. By doing this, many similar operations are moved around and closer to each other, which creates more opportinities for the algebraic simplification.

### Dataflow-level Optimizations
#### Common Sub-expression Elimination (CSE)
If the expression is previously computed then it should be reused, the CSE optimization exploites this particular theory into practice by reusing alrady computed values of an operation.

### Dead Code Elimination (DCE)
If there is a part in computation graph that can't be reached not matter what input is passed to the graph, then it's called dead code. This optimization identifies these blocks which can't be reached and are eliminated. DCE does not effect in the performance of the model but reduces the size of the model. The dead code is usually occured in computation graph when some graph optimization is applied and makes other nodes unusable.

### Static Memory Planning
This optimziation is conducted to reuse the memory buffers as much as possible.

There are two approaches to achieve this:

- in-place memory sharing: it uses the same memory for input as well as output of the computation and only allocates one copy before computation.

- Standard memory sharing: it reuses the memory from pervious opeartions without overlapping.

### Layout Transformation

The optimization tries to find the best data layout to store tensors in the computation graph and then insert the layout transformation nodes to the computation graph. These are usually evaluated by compiler backends and are not performed in the computation grpah itself.

Depending on the hardware the data layout performance differs, so these can be applied based on the target hardware that you are expecting.
Not only the data layouts of input, output, and intermediate tensors have a nontrivial inﬂuence on
the ﬁnal performance, but also the transformation operations have a signiﬁcant overhead. Because
the transformation operations also consume the memory and computation resource.
