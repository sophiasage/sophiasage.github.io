# My First Blog Post 
---
## html presentations 

Today I learned how to make nice html presentations from markdown with 
[reveal.js](https://revealjs.com/).

----
# A new page appears
---
## Hello World


---
## A Subpage?

----
# There is math typesetting:
---
## `$ \langle \rangle $` 

---
## And Highlighting
  ```js [1-2|3|4]
      let a = 1;
      let b = 2;
      let c = x => 1 + 2 + x;
      c(3);
      ```
----
# Animations


---
Fade in <!-- .element: class="fragment" data-fragment-index="1" -->

Fade-out <!-- .element: class="fragment fade-out" data-fragment-index="2" -->

Highlight red <!-- .element: class="fragment highlight-red" data-fragment-index="3"  -->

fade-in-then-out <!-- .element: class="fragment fade-in-then-out" data-fragment-index="4"   -->

fade-up <!-- .element: class="fragment fade-up" data-fragment-index="6" -->

----
# Interpolation Notes
## The Full Chain

How a Half Band Interpolator works.

----

# The Discrete Fourier Transform

We start with 16 samples: `$ x[nT_s] \text{ for } n \ in [0,15]$`.

As we have 16 samples, `$\frac{1}{f_0} = T_0 = 16 T_s$`.

When we perform the IDFT and DFT on our signal, we get:

`$$ IDFT : x[nT_s] = \frac{1}{16} \sum_{k=0}^{15} X(k) e^{2 \pi i f_0 k n T_s} \\
                   = \frac{1}{16} \sum_{k=0}^{15} X(k) e^{\frac{2 \pi i k n}{16}} .$$`

Here we have expressed our signal in terms of a basis that consists of the 
16th roots of unity.

`$$  DFT : X(k)    = \sum_{n = 0}^{15} x[nT_s] e^{\frac{-2 \pi i k n f_0}{f_s} } \\
                   =  \sum_{n = 0}^{15} x[nT_s] e^{\frac{-2 \pi i k n}{16} }.$$`  

Here we found the component in our signal along the `$k$`-th root of unity.

----

# Resampling

We've expressed our sample in terms of a basis with 16 elements that is periodic
modulo `$2 \pi$`.

Our discrete-time frequency is:
`$$\hat{\omega} = \frac{2 \pi k }{16} = \frac{2 \pi k T_s}{T_s} = \frac{2 \pi k f_0}{f_s}. $$`

Thus, if we follow the sampling theorem and limit `$f_s \geq 2 f_0$`, then
`$k$` is constrained to lie between -7 and 8. 




