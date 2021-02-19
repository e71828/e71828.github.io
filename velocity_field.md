<head>
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
    <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            // show equation numbers
            TeX: {
                 equationNumbers: {  autoNumber: "AMS"  },
                 extensions: ["AMSmath.js"]
            },
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [['$','$']]
            }
        });
    </script>
</head>

# Incompressible flow

$$\nabla \cdot {\bf V} = 0 $$

or

\begin{equation}\label{eq:incompressible}
\frac{\partial u}{\partial x} + \frac{\partial v}{\partial y} = 0
\end{equation}

Equation \eqref{eq:incompressible} applies to both steady and unsteady flow of two-demensional incompressible fluids.

# The Stream Function
We still have two variables, $u$ and $v$ to deal with, but they must be related in a special way as indicated by Eq. \eqref{eq:incompressible}. This equation suggests that if we define a function $\psi(x,y)$, called the <i><font face="Times New Roman" size="5" color="orange"> stream function </font></i>, which relates the velocities shown by the figure in the margin as

\begin{equation}\label{eq:psi}
\bbox[10px, border: 2px solid orange]{u = \frac{\partial \psi}{\partial y}\qquad v = -\frac{\partial \psi}{\partial x}}
\end{equation}

Another particular advantage of using the stream function is related to the fact that _lines along which $\psi$ is constant are streamlines_. It follows from the definition of the streamline that the slope at any point
along a streamline is given by

$$\frac{\mathrm{d} y}{\mathrm{d} x} = \frac{v}{u}$$

# Irrotational flow

$$\nabla \times {\bf V} = 0 $$

therefore,

\begin{equation}\label{eq:irrotational}
\frac{\partial v}{\partial x} = \frac{\partial u}{\partial y}
\end{equation}

# The Velocity Potential

For a two-demensional irrotational flow the velocity gradients follows that in this case the velocity components can be expressed in terms of a scalar function $\phi(x,y,t)$ as

\begin{equation}\label{eq:phi}
u = \frac{\partial \phi}{\partial x} \qquad v = \frac{\partial \phi}{\partial y}
\end{equation}

In vector form, Eqs. \eqref{eq:phi} can be written as

\begin{equation}\label{eq:phi_v}
\bbox[10px, border: 2px solid orange]{\mathbf{V} = \nabla \phi}
\end{equation}

# Plane Potential Flows

平面复势：

\begin{equation}\label{eq:f}
f = \phi + i \,\psi
\end{equation}

并且，
\begin{equation}
V = \overline{f^\prime} = u + i \,v =\overline{f^\prime(z)}
\end{equation}

最后，
\begin{equation}
f^\prime(z) \Rightarrow f(z) \Rightarrow \phi\; \text{and} \;\psi
\end{equation}

# [HOME](../index.md)
