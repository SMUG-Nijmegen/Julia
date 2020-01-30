---
title : Julia
subtitle: A new programming language that shows you can have it all
author:
    - Phillip M. Alday
date  : 30 January 2020
lang: en-US
header-includes:
- \usepackage{csquotes}
- \usepackage{tikz}
- \definecolor{MPIorange}{HTML}{ED6B06}
- \definecolor{MPIcyan}{HTML}{00786A}
- \definecolor{MPIlcyan}{HTML}{80BBB4}
- \setbeamercolor{local structure}{fg=MPIorange}
- \setbeamercolor{structure}{fg=MPIcyan}
include-before:
- \tikzset{every picture/.append style={trim left=0}}
theme: Singapore
aspectratio: 169
mainfont: Gentium Basic
mainfontoptions: BoldFont=Gentium Basic Bold
mainfontoptions: ItalicFont=Gentium Basic Italic
mainfontoptions: BoldItalicFont=Gentium Basic Bold Italic
monofont: DejaVu Sans
---

# Have your cake and eat it too

## Because we're greedy

> We want a language that’s open source, with a liberal license. We want the speed of C with the dynamism of Ruby. We want a language that’s homoiconic, with true macros like Lisp, but with obvious, familiar mathematical notation like Matlab. We want something as usable for general programming as Python, as easy for statistics as R, as natural for string processing as Perl, as powerful for linear algebra as Matlab, as good at gluing programs together as the shell. Something that is dirt simple to learn, yet keeps the most serious hackers happy. We want it interactive and we want it compiled.
>
>
> (Did we mention it should be as fast as C?)

from [Why We Created Julia](https://julialang.org/blog/2012/02/why-we-created-julia/)

. . .

See also:
[Julia: The Goldilocks language](https://increment.com/programming-languages/goldilocks-language-history-of-julia/) and [Julia: come for the syntax, stay for the speed](https://www.nature.com/articles/d41586-019-02310-3)

## Core syntax looks like more familiar dynamics languages

~~~~{.julia}
julia> function pow(x,n)
    x^n
end
pow (generic function with 1 method)

~~~~~~~~~~~~~





If you have passing familiar with Python and MATLAB, Julia will seem very natural. But [there are a few differences](https://docs.julialang.org/en/v1/manual/noteworthy-differences/).

## Balancing compiling and interpreting: JIT compilation

~~~~{.julia}
julia> @time pow(2,2)
  0.006687 seconds (756 allocations: 45.413 KiB)
4

julia> @time pow(2,2)
  0.000003 seconds (4 allocations: 160 bytes)
4

~~~~~~~~~~~~~





## A little computer science goes a long way

~~~~{.julia}
julia> function pow(x::Number,n::Integer)
    # don't actually do this -- its horrible numerically
    if n < 0
        x = 1 / x
        n = -n
    end

    if n > 0
        return pow(x, n-1, x*x)
    elseif n == 1
        return x
    else
        return 1
    end
end
pow (generic function with 2 methods)

~~~~~~~~~~~~~





## A little computer science goes a long way
~~~~{.julia}
julia> function pow(x::T,n::Integer,result::T) where T <: Number
    if n > 0
        return pow(x, n-1, x * result)
    else
        return result
    end
end
pow (generic function with 3 methods)

julia> 
methods(pow)
# 3 methods for generic function "pow":
[1] pow(x::Number, n::Integer) in Main.WeaveSandBox0 at none:3
[2] pow(x::T, n::Integer, result::T) where T<:Number in Main.WeaveSandBox0 at none:2
[3] pow(x, n) in Main.WeaveSandBox0 at none:2

~~~~~~~~~~~~~





## A little computer science goes a long way

~~~~{.julia}
julia> pow(2,1024)
0

~~~~~~~~~~~~~



~~~~{.julia}
julia> # don't overflow when exponentiating ints
pow(x::Int,n::Int) = pow(BigInt(x), n)
pow (generic function with 4 methods)

~~~~~~~~~~~~~



~~~~{.julia}
julia> pow(2,1024)
359538626972463181545861038157804946723595395788461314546860162315465351611001926265416954644815072042240227759742786715317579537628833244985694861278948248755535786849730970552604439202492188238906165904170011537676301364684925762947826221081654474326701021369172596479894491876959432609670712659248448274432

~~~~~~~~~~~~~



~~~~{.julia}
julia> pow.([2,3,9],2)
3-element Array{BigInt,1}:
   8
  27
 729

~~~~~~~~~~~~~





## Installing Julia

The [Julia download site](https://julialang.org/downloads/) provides binary downloads for most common operating systems. Ensure that the version you install is at least v1.3.1. Version 1.4.0-rc1 (release candidate 1) is also suitable if you want to be in the vanguard.

## The Julia REPL

By itself the Julia binary provides a basic REPL (read-eval-print-loop), which is quite adequate for installing packages. (Installing and using IDE's for Julia is discussed below.) When you start Julia you are in julia mode, as indicated by the `julia> ` prompt.

Typing certain characters as the first in an input line will change the mode of the REPL.

| Character | Prompt | Context |
| --------- | ------ | --------------- |
| `?` | `help?> ` | Help mode - print help messages on functions, types, etc. |
| `]` | `(v1.3) pkg> ` | Pkg mode - install, list, remove, etc. packages |
| `;` | `shell> ` | Shell mode - execute a single shell command |
| `$` | `R> ` | R mode - requires `RCall` package installed and active |
| `<backspace>` | `julia> ` | return to Julia mode |

# Julia packages

## The Standard Library

~~~~{.julia}
julia> using InteractiveUtils, Random, Statistics

julia> varinfo(Random)   # list exported functions and types
  name                   size summary                   
  ––––––––––––––– ––––––––––– ––––––––––––––––––––––––––
  AbstractRNG       176 bytes DataType                  
  MersenneTwister   232 bytes DataType                  
  Random          429.749 KiB Module                    
  RandomDevice      200 bytes DataType                  
  bitrand             0 bytes typeof(Random.bitrand)    
  rand!               0 bytes typeof(Random.rand!)      
  randcycle           0 bytes typeof(Random.randcycle)  
  randcycle!          0 bytes typeof(Random.randcycle!) 
  randexp             0 bytes typeof(Random.randexp)    
  randexp!            0 bytes typeof(Random.randexp!)   
  randn!              0 bytes typeof(Random.randn!)     
  randperm            0 bytes typeof(Random.randperm)   
  randperm!           0 bytes typeof(Random.randperm!)  
  randstring          0 bytes typeof(Random.randstring) 
  randsubseq          0 bytes typeof(Random.randsubseq) 
  randsubseq!         0 bytes typeof(Random.randsubseq!)
  shuffle             0 bytes typeof(Random.shuffle)    
  shuffle!            0 bytes typeof(Random.shuffle!)   

julia> varinfo(Statistics)
  name              size summary                     
  –––––––––– ––––––––––– ––––––––––––––––––––––––––––
  Statistics 207.796 KiB Module                      
  cor            0 bytes typeof(Statistics.cor)      
  cov            0 bytes typeof(Statistics.cov)      
  mean           0 bytes typeof(Statistics.mean)     
  mean!          0 bytes typeof(Statistics.mean!)    
  median         0 bytes typeof(Statistics.median)   
  median!        0 bytes typeof(Statistics.median!)  
  middle         0 bytes typeof(Statistics.middle)   
  quantile       0 bytes typeof(Statistics.quantile) 
  quantile!      0 bytes typeof(Statistics.quantile!)
  std            0 bytes typeof(Statistics.std)      
  stdm           0 bytes typeof(Statistics.stdm)     
  var            0 bytes typeof(Statistics.var)      
  varm           0 bytes typeof(Statistics.varm)     

~~~~~~~~~~~~~





## Some Relevant User-contributed Packages

| Name | Purpose |
| ---- | ----------- |
| CSV | read and write comma-separated-value files |
| CategoricalArrays | `factor`-like objects |
| DataFrames | data tables with properties and capabilities like R's `data.frame` |
| DataFramesMeta | database-like operations on data tables |
| IJulia | Run Julia in Jupyter notebooks }
| MixedModels | fit and examine mixed-effects models |
| PooledArrays | light-weight version of categorical arrays |
| RCall | call R from Julia, including data transfers |
| RData | read data tables stored in `.rda` or `.rds` format |
| Tables | general data table structures - either row- or column-oriented |
| Weave | similar to the `knitr` package for R |

These packages must be added  before they can be attached with `using` (similar to `library` or `require` in R).  In the package REPL (accessed by typing `]` as the first character of a line) just type `add Tables`, for example.

## Integrated Development Environments

Editing and running Julia code is supported in both the [Atom editor](https://atom.io) and [VSCode](https://code.visualstudio.com).  The Atom support is called Juno and is documented at https://junolab.github.io.  The VSCode support is documented at https://www.julia-vscode.org

Either of these environments is fine for this workshop.

## Jupyter notebooks

[Jupyter](https://jupyter.org) provides interactive notebooks in the browser.  When the `IJulia` package is installed and built it installs a version of [`conda`](https://docs.conda.io/en/latest/) if it is not already available.  The `jupyter-notebook` and `jupyter-lab` applications can be added through conda.  If you haven't used Jupyter notebooks it is probably best to wait to install them at the workshop.

## Weave

Like `knitr` for RMarkdown, you can turn Julia Markdown to vanilla Markdown:

~~~~{.julia}

using Weave
weave("introducting-julia.jmd", doctype="pandoc")
~~~~~~~~~~~~~




```{bash;term=true;eval=false}
pandoc introducing-julia.md --to beamer --pdf-engine=xelatex -o introducing-julia.pdf

```

. . .

But you can also produce IPython Notebooks for interactive teaching, while still maintaining version control friendly `jmd` as the canonical form:

~~~~{.julia}

using Weave
convert_doc("introducing-julia.jmd", "introducing-julia.ipynb")
~~~~~~~~~~~~~




# RCall: Or How I Learned to Stop Worrying and Love Getting Stuff Done

## You can access R objects directly from within Julia

~~~~{.julia}
julia> using RCall

julia> R"lme4::sleepstudy"
RCall.RObject{RCall.VecSxp}
    Reaction Days Subject
1   249.5600    0     308
2   258.7047    1     308
3   250.8006    2     308
4   321.4398    3     308
5   356.8519    4     308
6   414.6901    5     308
7   382.2038    6     308
8   290.1486    7     308
9   430.5853    8     308
10  466.3535    9     308
11  222.7339    0     309
12  205.2658    1     309
13  202.9778    2     309
14  204.7070    3     309
15  207.7161    4     309
16  215.9618    5     309
17  213.6303    6     309
18  217.7272    7     309
19  224.2957    8     309
20  237.3142    9     309
21  199.0539    0     310
22  194.3322    1     310
23  234.3200    2     310
24  232.8416    3     310
25  229.3074    4     310
26  220.4579    5     310
27  235.4208    6     310
28  255.7511    7     310
29  261.0125    8     310
30  247.5153    9     310
31  321.5426    0     330
32  300.4002    1     330
33  283.8565    2     330
34  285.1330    3     330
35  285.7973    4     330
36  297.5855    5     330
37  280.2396    6     330
38  318.2613    7     330
39  305.3495    8     330
40  354.0487    9     330
41  287.6079    0     331
42  285.0000    1     331
43  301.8206    2     331
44  320.1153    3     331
45  316.2773    4     331
46  293.3187    5     331
47  290.0750    6     331
48  334.8177    7     331
49  293.7469    8     331
50  371.5811    9     331
51  234.8606    0     332
52  242.8118    1     332
53  272.9613    2     332
54  309.7688    3     332
55  317.4629    4     332
56  309.9976    5     332
57  454.1619    6     332
58  346.8311    7     332
59  330.3003    8     332
60  253.8644    9     332
61  283.8424    0     333
62  289.5550    1     333
63  276.7693    2     333
64  299.8097    3     333
65  297.1710    4     333
66  338.1665    5     333
67  332.0265    6     333
68  348.8399    7     333
69  333.3600    8     333
70  362.0428    9     333
71  265.4731    0     334
72  276.2012    1     334
73  243.3647    2     334
74  254.6723    3     334
75  279.0244    4     334
76  284.1912    5     334
77  305.5248    6     334
78  331.5229    7     334
79  335.7469    8     334
80  377.2990    9     334
81  241.6083    0     335
82  273.9472    1     335
83  254.4907    2     335
84  270.8021    3     335
85  251.4519    4     335
86  254.6362    5     335
87  245.4523    6     335
88  235.3110    7     335
89  235.7541    8     335
90  237.2466    9     335
91  312.3666    0     337
92  313.8058    1     337
93  291.6112    2     337
94  346.1222    3     337
95  365.7324    4     337
96  391.8385    5     337
97  404.2601    6     337
98  416.6923    7     337
99  455.8643    8     337
100 458.9167    9     337
101 236.1032    0     349
102 230.3167    1     349
103 238.9256    2     349
104 254.9220    3     349
105 250.7103    4     349
106 269.7744    5     349
107 281.5648    6     349
108 308.1020    7     349
109 336.2806    8     349
110 351.6451    9     349
111 256.2968    0     350
112 243.4543    1     350
113 256.2046    2     350
114 255.5271    3     350
115 268.9165    4     350
116 329.7247    5     350
117 379.4445    6     350
118 362.9184    7     350
119 394.4872    8     350
120 389.0527    9     350
121 250.5265    0     351
122 300.0576    1     351
123 269.8939    2     351
124 280.5891    3     351
125 271.8274    4     351
126 304.6336    5     351
127 287.7466    6     351
128 266.5955    7     351
129 321.5418    8     351
130 347.5655    9     351
131 221.6771    0     352
132 298.1939    1     352
133 326.8785    2     352
134 346.8555    3     352
135 348.7402    4     352
136 352.8287    5     352
137 354.4266    6     352
138 360.4326    7     352
139 375.6406    8     352
140 388.5417    9     352
141 271.9235    0     369
142 268.4369    1     369
143 257.2424    2     369
144 277.6566    3     369
145 314.8222    4     369
146 317.2135    5     369
147 298.1353    6     369
148 348.1229    7     369
149 340.2800    8     369
150 366.5131    9     369
151 225.2640    0     370
152 234.5235    1     370
153 238.9008    2     370
154 240.4730    3     370
155 267.5373    4     370
156 344.1937    5     370
157 281.1481    6     370
158 347.5855    7     370
159 365.1630    8     370
160 372.2288    9     370
161 269.8804    0     371
162 272.4428    1     371
163 277.8989    2     371
164 281.7895    3     371
165 279.1705    4     371
166 284.5120    5     371
167 259.2658    6     371
168 304.6306    7     371
169 350.7807    8     371
170 369.4692    9     371
171 269.4117    0     372
172 273.4740    1     372
173 297.5968    2     372
174 310.6316    3     372
175 287.1726    4     372
176 329.6076    5     372
177 334.4818    6     372
178 343.2199    7     372
179 369.1417    8     372
180 364.1236    9     372


~~~~~~~~~~~~~





## You can access R objects directly from within Julia

~~~~{.julia}
julia> using RCall, DataFrames

julia> sleep = rcopy(R"lme4::sleepstudy");

julia> first(sleep, 6)
6×3 DataFrames.DataFrame
│ Row │ Reaction │ Days    │ Subject      │
│     │ Float64  │ Float64 │ Categorical… │
├─────┼──────────┼─────────┼──────────────┤
│ 1   │ 249.56   │ 0.0     │ 308          │
│ 2   │ 258.705  │ 1.0     │ 308          │
│ 3   │ 250.801  │ 2.0     │ 308          │
│ 4   │ 321.44   │ 3.0     │ 308          │
│ 5   │ 356.852  │ 4.0     │ 308          │
│ 6   │ 414.69   │ 5.0     │ 308          │

~~~~~~~~~~~~~





# Fine Print

## A couple of gotchas:

- variable semantics are like Pythons (pass by reference with immutable numeric and string types)
- there is a difference between single and double quotes and characters and stringss
- the numeric type system is *much* richer than either Python or R
~~~~{.julia}
julia> π
π = 3.1415926535897...

julia> typeof(π)
Irrational{:π}

~~~~~~~~~~~~~




- one-based indexing (like R and MATLAB)


## Good references
- [Quick Reference](https://juliadocs.github.io/Julia-Cheat-Sheet/)
- [Unicode Reference](https://docs.julialang.org/en/v1/manual/unicode-input/)
- [FAQs](https://docs.julialang.org/en/v1/manual/faq/)
- [Noteworthy differences from other languages](https://docs.julialang.org/en/v1/manual/noteworthy-differences/)
- [Weave](http://weavejl.mpastell.com/stable/)
- [Performance tips](https://docs.julialang.org/en/v1/manual/performance-tips/)
- [Style Guide (also relevant for performance)](https://docs.julialang.org/en/v1/manual/style-guide/)
- [Workflow tips](https://docs.julialang.org/en/v1/manual/workflow-tips/)

# Now some live coding using MixedModels.jl

## huge datasets

~~~~{.julia}
using Random, DataFrames, MixedModels, PooledArrays
gendata(n::Int, nID::Int, nested=true) = gendata(MersenneTwister(42), n, nID, nested)

function gendata(rng::AbstractRNG, n::Int, nID::Int, nested::Bool)
    df = DataFrame(y = zeros(n),
                   w = rand(rng, n),
                   x = rand(rng, n),
                   z = rand(rng, n),
                   sex = CategoricalArray([randstring(rng,"mf",1) for ii in 1:n]),
                   group = CategoricalArray(rand(rng, 1:5, n)),
                   ID = CategoricalArray(rand(rng, 1:nID, n)))

    uID = DataFrame(ID = unique(df.ID),
                    u = randn(rng, nID))
    if nested
        uID.city = CategoricalArray(rand(rng, 1:30, length(unique(df.ID))))
    else
        df.city = CategoricalArray(rand(rng, 1:30, n))
    end

    df = join(df, uID, on=:ID)
    # set all betas for continous to 1 and normal(0,1) residuals
    # group isn't further specified, so it's just noise
    df.y = 1 .+ df.w + df.x + df.z + (df.sex .== "m") + df.u + randn(rng,n)

    # there aren't many levels of city, so stepping through them makes more
    # sense than doing another join
    for cc in unique(df.city)
        df[df.city .== cc, :y] = df[df.city .== cc, :y] .+ randn(rng)
    end

    df
end
~~~~~~~~~~~~~


~~~~
gendata (generic function with 3 methods)
~~~~



~~~~{.julia}

@time df = gendata(100,10);
~~~~~~~~~~~~~


~~~~{.julia}

@time warmup = LinearMixedModel(@formula(y ~ 1 + x + w + z + sex + group + (1 | ID) + (1|city)), df);
~~~~~~~~~~~~~


~~~~{.julia}

@time fit!(warmup)
~~~~~~~~~~~~~


~~~~{.julia}

@time df = gendata(1000,10);
~~~~~~~~~~~~~


~~~~{.julia}

@time msmall = LinearMixedModel(@formula(y ~ 1 + x + w + z + sex + group + (1 | ID) + (1|city)), df);
~~~~~~~~~~~~~


~~~~{.julia}

@time fit!(msmall)
~~~~~~~~~~~~~


~~~~{.julia}

@time df = gendata(1000000,1000);
~~~~~~~~~~~~~


~~~~{.julia}

@time mbig = LinearMixedModel(@formula(y ~ 1 + x + w + z + sex + group + (1 | ID) + (1|city)), df);
~~~~~~~~~~~~~


~~~~{.julia}

@time fit!(mbig)
~~~~~~~~~~~~~


~~~~{.julia}

@time df = gendata(2000000,5000);
~~~~~~~~~~~~~


~~~~{.julia}

@time mhuge = LinearMixedModel(@formula(y ~ 1 + x + w + z + sex + group + (1 | ID) + (1|city)), df);
~~~~~~~~~~~~~


~~~~{.julia}

@time fit!(mhuge)
~~~~~~~~~~~~~




## Using RCall in the REPL to fit a model in Julia and manipulate it in R


~~~~{.julia}

usng RCall, DataFrames, MixedModels
insteval = rcopy(R"lme4::InstEval")
@time mod = fit(MixedModel,@formula(y ~ service  + (1|d) + (1|s) + (1|dept)),insteval,verbose=true)
mod.θ
R> library("lme4")
R> theta <- $(mod.θ)
R> system.time(
mod_julia <- lmer(y ~ service + (1|d) + (1|s) + (1|dept), InstEval, REML=FALSE, start=theta, control=lmerControl("nloptwrap",optCtrl=list(maxeval=1),calc.derivs=FALSE)))
R> library(car)
R> Anova(mod_julia )
R> plot(mod_julia )
R> library("lattice")
R> qqmath(mod_julia )
R> system.time(
modR <- lmer(y ~ service + (1|d) + (1|s) + (1|dept), InstEval, REML=FALSE, control=lmerControl("nloptwrap",calc.derivs=FALSE)))
# be wary of contrast coding
@time mod = fit(MixedModel,@formula(y ~ service + lectage + studage + (1|d) + (1|s) + (1|dept)),insteval,verbose=true)
modR <- lmer(y ~ service + lectage + studage + (1|d) + (1|s) + (1|dept), InstEval, REML=FALSE, control=lmerControl("nloptwrap",calc.derivs=FALSE)))
# doesn't work
# @time mod2= fit(MixedModel,@formula(y ~ service + (1+service|d) + (1|s) + (1|dept)),insteval,verbose=true)
~~~~~~~~~~~~~

