.. -*- coding: utf-8 -*-

.. linkall

.. _sections:

=========================================
An Introduction to Ehrhart Theory in Sage
=========================================

.. MODULEAUTHOR:: Sophia Elia <sophiae56@math.fu-berlin.de>

This tutorial aims to showcase some of the possibilities
of Sage concerning Ehrhart Theory.

The classic literature on the topic includes:

- *Computing the Continuous Discretely*, Beck and Robins. [BR15]_

This document was prepared during the Sage Days 98 in Archanes, Crete and
the Sage Days 100 in Bonn.

.. contents:: Lectures Menu
    :depth: 2

Lecture 0 - Lattice Polytopes
==============================

Ehrhart theory allows one to count lattice points in polytopes.
To see how to construct polytopes in Sage, look at the following tutorial:

:ref:`polyhedra_tutorial`

Lecture 1 - Ehrhart Polynomials
================================

Let `P \subseteq mathbb R^n` be a `d`-dimensional lattice polytope 
(we may assume the lattice is `\mathbb Z^n` and that all of `P`'s vertices have
integral coordinates). 
The *Ehrhart counting function* `ehr_P(k)` evaluated at a 
positive integer `k` is equal to the number of lattice points in the 
`k`-th dilate of `P`:

.. MATH::

    ehr_P(k) = \lvert kP \cap \mathbb Z ^n \lvert.

In 1962 Eug`\`{e}`ne Ehrhart proved that the Ehrhart counting function is 
given by a rational polynomial in `k` of degree equal to the dimension of the 
polytope, `d`.

.. MATH::

    ehr_P(k) = c_d k^d + c_{d-1} k^{d-1} + \dots + c_1 k + c_0.

The coefficients of the Ehrhart polynomial have meaning in terms of the 
polytope.  The leading coefficient `c_d` is equal to the volume of `P`
(with respect to its affine hull). 
For example, the leading coefficient of Ehrhart polynomial 
of the tetrahedron with vertices `\{0,e_i\}` is `\frac{1}{6}`. 

::

    sage: simplex = Polyhedron(vertices = [[0,0,0],[1,0,0],[0,1,0],[0,0,1]])
    sage: simplex.ehrhart_polynomial()
    1/6*t^3 + t^2 + 11/6*t + 1
    sage: simplex.affine_hull().volume()
    1/6

.. end of output

The second coefficient is equal to half of the polytope's 
surface area with respect to the sublattice on the facets of `P`. 
The constant coefficient is one. For example, the simplex from above
has four facets, each of surface area 1/2, so the second coefficient is 1::

    sage: surf_area = 0 
    sage: for facet in simplex.faces(2):
    ....:     facet = facet.as_polyhedron()
    ....:     surf_area += facet.affine_hull().volume()
    sage: surf_area
    2

Ehrhart computations in Sage currently rely on the LattE Integrale
and Normaliz optional packages.

.. SEEALSO::

    :mod:`~sage.interfaces.latte` the interface to LattE Integrale

You can to install Latte using the terminal command:: 
    $sage -i latte_int 
Likewise, Normaliz may be installed using the terminal command::
    $sage -i pynormaliz

Examples
-------------------
The ``ehrhart_polynomial`` function has two inputs ``engine`` and 
``variable``. The ``engine`` option allows you to choose which 
backed you'd like to use to compute the ehrhart polynomial. The 
options are ``'latte'`` and ``'normaliz'``. To compute the 
Ehrhart polynomial with Normaliz, the backend of the polytope must
be ``'normaliz'``. You can set the ``variable`` to a different 
string if you want the Ehrhart polynomial to be over another variable.
To start, we find the Ehrhart polynomial of a three-dimensional
``simplex``, first using ``engine='latte'``. Leaving the engine
unspecified sets the ``engine`` to 'latte' by default::
        
    sage: simplex = Polyhedron(vertices=[(0,0,0),(3,3,3),(-3,2,1),(1,-1,-2)])
    sage: poly = simplex.ehrhart_polynomial(engine = 'latte')  # optional - latte_int
    sage: poly                         
    7/2*t^3 + 2*t^2 - 1/2*t + 1
    sage: poly(1)                          
    6
    sage: len(simplex.integral_points())
    6
    sage: poly(2)  
    36
    sage: len((2*simplex).integral_points())
    36
        
Now we find the same Ehrhart polynomial, this time using
``engine ='normaliz'``. To use the Normaliz engine, the ``simplex`` must 
be defined with ``backend = 'normaliz'``::

    sage: simplex = Polyhedron(vertices=[(0,0,0),(3,3,3),(-3,2,1),(1,-1,-2)], backend='normaliz') #optioanl - pynormaliz
    sage: poly = simplex.ehrhart_polynomial(engine = 'normaliz') #optional - pynormaliz
    sage: poly                         
    7/2*t^3 + 2*t^2 - 1/2*t + 1

If the ``engine='normaliz'``, the backend should be ``'normaliz'``, otherwise
it returns an error::

    sage: simplex = Polyhedron(vertices=[(0,0,0),(3,3,3),(-3,2,1),(1,-1,-2)])
    sage: simplex.ehrhart_polynomial(engine='normaliz')
    Traceback (most recent call last):
    ...
    TypeError: The polyhedron's backend should be 'normaliz'

Now we find the Ehrhart polynomials of the unit hypercubes of 
dimensions three through six. They are computed first with 
``engine='latte'`` and then with ``engine='normaliz'``. 
The degree of the Ehrhart polynomial matches the dimension of the 
hypercube, and the coefficient of the leading monomial equals the 
volume of the unit hypercube::

    sage: from itertools import product
    sage: def hypercube(d):
    ....:     return Polyhedron(vertices=list(product([0,1],repeat=d)))
    sage: hypercube(3).ehrhart_polynomial()   # optional - latte_int
    t^3 + 3*t^2 + 3*t + 1
    sage: hypercube(4).ehrhart_polynomial()   
    t^4 + 4*t^3 + 6*t^2 + 4*t + 1
    sage: hypercube(5).ehrhart_polynomial()  
    t^5 + 5*t^4 + 10*t^3 + 10*t^2 + 5*t + 1
    sage: hypercube(6).ehrhart_polynomial()
    t^6 + 6*t^5 + 15*t^4 + 20*t^3 + 15*t^2 + 6*t + 1

    sage: def hypercube(d):
    ....:     return Polyhedron(vertices=list(product([0,1],repeat=d)),backend='normaliz') # optional - pynormaliz
    sage: hypercube(3).ehrhart_polynomial(engine='normaliz')   
    t^3 + 3*t^2 + 3*t + 1
    sage: hypercube(4).ehrhart_polynomial(engine='normaliz')  
    t^4 + 4*t^3 + 6*t^2 + 4*t + 1
    sage: hypercube(5).ehrhart_polynomial(engine='normaliz') 
    t^5 + 5*t^4 + 10*t^3 + 10*t^2 + 5*t + 1
    sage: hypercube(6).ehrhart_polynomial(engine='normaliz')  
    t^6 + 6*t^5 + 15*t^4 + 20*t^3 + 15*t^2 + 6*t + 1

An empty polyhedron::

    sage: P = Polyhedron(ambient_dim=3, vertices=[])
    sage: P.ehrhart_polynomial()
    0
    sage: parent(_)                
    Univariate Polynomial Ring in t over Rational Field

The ``ehrhart_polynomial`` function can also be used to find the Ehrhart 
polynomials of polytopes defined over ``\QQ``:

    sage: simplex = Polyhedron(vertices=[(0,0,0),(3,3,3),(-3,2,1),(1,-1,-2)])
    sage: simplex = simplex.change_ring(QQ)
    sage: poly = simplex.ehrhart_polynomial(engine = 'latte')  #optional-latte_int
    sage: poly                         
    7/2*t^3 + 2*t^2 - 1/2*t + 1

Rational Polytopes
--------------------

The Ehrhart counting function of a polytope `P` with rational vertices is given
by a *quasi-polynomial*. That is, there exists a positive integer `l` and `l`
polynomials `ehr_{P,i} \text{ for } i \in \{1,\dots,l \}` such that if `t` is 
equivalent to `i` mod `l` then `tP \cap \mathbb Z^d = ehr_{P,i}(t)`. 

As an example, consider the line segment `LS` = [0,1/2]. If we dilate this
line segment by an even integer factor `k`, then the dilated line segment will 
contain `k/2 +1` lattice points. If `k` is odd then there will be `k/2+1/2` 
lattice points in the dilated line segment. We obtain this result in Sage as 
follows. Note that it is necessary to set the backend of the polytope to 
'normaliz':

:: 

    sage: Line_Seg = Polyhedron(vertices=[[0],[1/2]],backend='normaliz')
    sage: Line_Seg
    A 1-dimensional polyhedron in QQ^1 defined as the convex hull of 2 vertices
    sage: Line_Seg.ehrhart_quasipolynomial()
    (1/2*t + 1, 1/2*t + 1/2)

For a more exciting example, let us look at the subpolytope of the 
3 dimensional permutahedron fixed by the reflection across the hyperplane `x_1 = x_4`::

    sage: subpoly = Polyhedron(vertices = [[3/2, 3, 4, 3/2],
    ....:  [3/2, 4, 3, 3/2],
    ....:  [5/2, 1, 4, 5/2],
    ....:  [5/2, 4, 1, 5/2],
    ....:  [7/2, 1, 2, 7/2],
    ....:  [7/2, 2, 1, 7/2]], backend = 'normaliz')
    sage: eq = subpoly.ehrhart_quasipolynomial()
    sage: eq
    (4*t^2 + 3*t + 1, 4*t^2 + 2*t)
    sage: eq = subpoly.ehrhart_quasipolynomial()
    sage: eq
    (4*t^2 + 3*t + 1, 4*t^2 + 2*t)
    sage: even_ep = eq[0]
    sage: odd_ep  = eq[1]
    sage: even_ep(2)
    23
    sage: ts = 2*subpoly
    sage: ts.integral_points_count()
    23
    sage: odd_ep(1)
    6
    sage: subpoly.integral_points_count()
    6

.. end of output

Lecture 2 - The Ehrhart Series
===================================

The *Ehrhart Series* of a `d`-dimensional  polytope `P` is the following 
generating function:

.. MATH::

    Ehr_P(t) =1 + \sum_{k>=1} ehr_P(k) t^k. 

In the Ehrhart series, the coefficient of `t^k` is equal to the Ehrhart 
polynomial of `P` evaluated at `k`; it is the number of lattice points in the
`k`-th dilate of `P`. If `P` is a lattice polytope, the Ehrhart series has the
following expression as a rational function:

.. MATH::

    Ehr_P(t) = \frac{h^{*}(t)}{(1-t)^{d+1}},

where the expression `h^{*}(t)` in the numerator is the *`h^*`-polynomial*
of `P`. 

We can compute the Ehrhart series of a lattice polytope in Sage. For this 
example, we compute the Ehrhart series of the octahedron. Notice that the 
denominator is equal to `(1-t)^4` since the octahedron is a lattice polytope
of dimension three. 
Further, the numerator is the same as the `h`-vector of the octahedron because 
the octahedron is a simplicial polytopes that admits a unimodular triangulation.

::

    sage: Oct = polytopes.octahedron(backend='normaliz')
    sage: Oct
    A 3-dimensional polyhedron in ZZ^3 defined as the convex hull of 6 vertices
    sage: ehr_ser = Oct.ehrhart_series()
    sage: ehr_ser
    (t^3 + 3*t^2 + 3*t + 1)/(t^4 - 4*t^3 + 6*t^2 - 4*t + 1)

.. end of output

The Ehrhart series is an especially useful tool and may be used to show 
the polynomality of the Ehrhart counting function. When working with rational
polytopes, it is often nicer to work with the Ehrhart series, because one 
avoids juggling a bunch of polynomials. In the case of rational polytopes,
the denominator no longer has such a simple expression. It is a product of 
(1-t^i)s, where `i` is an integer. 

As an example, let us look at the Ehrhart series of the line segment [0,1/2].

::

    sage: LS = Polyhedron(vertices = [[0],[1/2]],backend = 'normaliz')
    sage: LS
    A 1-dimensional polyhedron in QQ^1 defined as the convex hull of 2 vertices
    sage: ehr_LS = LS.ehrhart_series()
    sage: ehr_LS
    1/(t^3 - t^2 - t + 1)
    sage: ehr_LS.denominator().factor()
    (t + 1) * (t - 1)^2

.. end of output


Lecture 3 - Fundamental Parallelepipeds and the `h^*` Polynomial 
===================================================================

Fundamental Parallelepipeds
----------------------------

The notion of a *fundamental parallelepiped* may be used to show that 
the Ehrhart series of a lattice polytope has an expression as a rational function.
Let `V` be a set of `n` linearly independent vectors in `\mathbb R^d` with 
integral coordinates. The *fundamental parallelepiped* of `V` is the set

.. MATH::

    \{ x \in \mathbb R^d \text{ such that } x = \lambda_1 v_1 + \dots + \lambda_n v_n, 
    \lambda_i \in [0,1)\, \forall i \}.

It is useful to know which lattice points are in the fundamental parallelepiped.
This can be used to calculate the `h^*` vector and thus the Ehrhart series.
In Sage one can calculate which lattice points are in the fundamental parallelepiped
as follows. Let us consider the vectors `(0,1)` and `(2,1)`.
There are two lattice points in the fundamental parallelepiped`: (0,0) and (1,1).

.. WARNING::

    This functionality depends on my function for calculating the fundamental
    parallelepiped. I haven't started a ticket yet.

:: 

    sage: vectors = [[0,1],[2,1]]
    sage: points = int_points_in_fund_par(vectors)
    sage: points
    [(0, 0), (1, 1)]

.. end of output

In order to calculate the `h^*`-vector one must be able to find the lattice 
points in the fundamental parallelepiped with respect to a 
*half-open decomposition* - insert definition. 

We can also compute the integral lattice points in the fundamental 
parallelepiped with respect to a half-open decomposition. Let us look at the 
lattice points in the fundamental parallelepiped of (0,1) and (2,1) with 
respect to the half open decomposition determined by the point (-1,1). 

::

    sage: vectors = [[0,1],[2,1]]
    sage: generic_point = vector([-1,1])
    sage: points = int_points_in_fund_par(vectors,generic_point)
    sage: points
    [(1, 1), (2, 1)]

.. end of output

The `h^*`-polynomial
----------------------

The `h^*-` *polynomial* is the numerator of the rational expression of the 
Ehrhart series of a `d`-dimensional lattice polytope `P`. It enjoys the 
following nice properties:

1. `ehr_P(k) = \sum_{i = 0 }^{d} h^*_i{{t +d -i}\choose{d}}` 
2. `\sum_{i = 1}^{d} h^*_i = \frac{Vol(P)}{d!}`
3. `h^*_0 = 1`
4. `h_d^* = ehr_P(-1) = |int(P) \cap \mathbb Z^d|`

We have already seen how to calculate the Ehrhart series of a polytope. 
The `h^*`-polynomial is just the numerator. Let us look at example 
to see properties (2) in action. Let ``cyclicp`` be the cyclic polytope (3,6). 

::

    sage: cyclicp = polytopes.cyclic_polytope(3,6, backend = 'normaliz')
    sage: cyclicp
    A 3-dimensional polyhedron in QQ^3 defined as the convex hull of 6 vertices
    sage: h = cyclicp.ehrhart_series().numerator()
    sage: h
    54*t^3 + 273*t^2 + 92*t + 1
    sage: h(1)/6
    70
    sage: cyclicp.volume()
    70

.. end of output

Lecture 4 - Equivariant Ehrhart Theory
==========================================

This lecture follows *Equivariant Ehrhart Theory*  by Alan Stapledon [Sta11]_.

The Setup: 
----------------

Let `P` be a `d`-dimensional lattice polytope at height one
invariant under the linear action of a group `G`. Let `\rho` be the matrix 
representation of the action of `G`.
Let `\chi_{mP}` be the character of the induced permuation 
action on the vector space who basis is a the set of lattice
points in `mP`. 

The *Equivariant Ehrhart Series* is the formal power series

.. MATH::

    EE_{P,G}(t) =  \sum_{m \geq 0} \chi_{mP}t^m.

Each coefficient of `t^m` in this sum is a function from the group `G` to the 
complex numbers that is constant on conjugacy classes, in other words it is a 
*class function*. When evaluated at a group element, `\chi_{mP}(g)` 
returns the number of lattice points in the `m`-th dilate of `P` fixed by `g`.
Plugging in a group element to this series returns the Ehrhart series of the 
subpolytope of `P` fixed by that group element.

As a first step, we can use Sage to return all of the fixed subpolytopes for a 
given group action on a polytope as above. Let `Cube` be the `\pm 1`-cube and `G`
be its automorphism group. Then we calculate a fixed subpolytope for each 
conjugacy class as follows:

::



.. end of output

The `\phi` Function
-------------------------

The Equivariant Ehrhart series has an expression as a rational function:

.. MATH::

    EE_{P,G}(t) = \frac{\phi(t)}{det(Id - \rho t )}

The function `\phi` in the numerator is a rational function in `t` with 
coefficients in the ring of class functions of `G`. The function `\rho` in the
denominator is the matrix representation of the group `G` on the polytope's 
ambient vector space.

An open problem in Equivariant Ehrhart theory is to determine when `\phi` is a
polynomial. We can have sage tell us `\phi`. 



Lecture 5 - Tropical Ehrhart Theory
=======================================

Bibliography
==============

.. [BR15] Matthias Beck and Sinai Robins. Computing the continuous discretely.
          Undergraduate Texts in Mathematics. Springer, New York, second 
          edition, 2015. Integer-point enumeration in polyhedra, With 
          illustrations by David Austin.

.. [Sta11] Alan Stapledon. Equivariant Ehrhart theory. Adv. Math., 226(4):3622-
           3654, 2011.
