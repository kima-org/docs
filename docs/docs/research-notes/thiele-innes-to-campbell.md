---
title: Thiele-Innes to Campbell Orbital Elements
date: 2026
authors:
  - name: Faria, J. P.
    orcid: 0000-0002-6728-244X

extra_citation:
  - text: and, for astrometric fitting,
    cite: Baycroft, Faria & Delisle (2026)
    url: https://ui.adsabs.harvard.edu/abs/2026arXiv260624132B

filled: true
---

# Thiele-Innes to Campbell Orbital Elements


<div markdown="1" class="no-print">

!!! quote "Cite as"

    Faria, J. P. [:fontawesome-brands-orcid:{ .orcid }](https://orcid.org/0000-0002-6728-244X)
    (2026). *Thiele-Innes to Campbell orbital elements* 
    (kima Research Notes). Zenodo.  
    
    DOI: [10.5072/zenodo.614181](https://handle.test.datacite.org/10.5072/zenodo.614181){:target="_blank"}  
    License: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/){:target="_blank"}


    If you use **kima**, please also cite 
    [Faria et al. (2018)](https://ui.adsabs.harvard.edu/abs/2018JOSS....3..487F){:target="_blank"}
    and, for astrometric fitting,
    [Baycroft, Faria & Delisle (2026)](https://ui.adsabs.harvard.edu/abs/2026arXiv260624132B){:target="_blank"}.

    ??? note "BibTeX"

        ```bibtex
        @misc{surname2026thiele,
          author    = {Faria, J.~P.},
          title     = {Thiele-Innes to Campbell orbital elements},
          year      = {2026},
          publisher = {Zenodo},
          doi       = {10.5072/zenodo.614181},
          url       = {https://handle.test.datacite.org/10.5072/zenodo.614181}
        }
        ```

</div>

<p class="print-only">
<strong>Author:</strong> J. P. Faria
</br>
<strong>License:</strong> 
<a href="https://creativecommons.org/licenses/by/4.0/" target="_blank">CC BY 4.0</a>
</p>

## Introduction

An orbit can be parametrised by several equivalent sets of orbital *elements*.
This note describes the conversion between the **Campbell** (geometric) elements
$(a, i, \Omega, \omega)$, and the **Thiele-Innes** (TI) elements $(A, B, F, G)$,
and vice versa. The first set is easier to interpret physically and
geometrically, while the second makes an astrometric orbital model (or part of
it) linear, and is the form in which, e.g., the Gaia mission publishes
astrometric orbits.

The TI elements are named after Thiele (1883)[^1], who introduced the idea
(although not in the form presented here), and Innes (1926)[^2] who developed
the method further (see van den Bos 1926[^3]). The Campbell elements are, to the
best of the author's knowledge, named after William Wallace 
Campbell$^*${title="Although an original reference or first use of this attribution is not known." class="no-print"}. 
The inversion presented here follows closely Appendix A of Halbwachs
et al. (2023)[^4], who in turn follow Binnendijk (1960)[^6]; the geometric
interpretation and the numerical remarks are additions.


Note that only the four elements describing the *size* and *orientation* of the
orbit are involved. The eccentricity $e$, the orbital period $P$ and the time of
periastron $T_p$ are the same in the two parametrisations and are not affected
by the conversion.

## The Thiele-Innes elements

Let $E$ be the [eccentric
anomaly](https://en.wikipedia.org/wiki/Eccentric_anomaly) and define the
position of the body in the orbital plane (in units of $a$), with $X$ pointing
towards periastron and $Y$ perpendicular to it, in the direction of motion: 
$$ X = \cos E - e, \qquad Y = \sqrt{1 - e^2}\,\sin E . $$

The offsets of the body's position on the plane of the sky, towards the north
($\Delta\delta$) and towards the east ($\Delta\alpha^*$), are then
<!--  -->
$$
\begin{aligned}
\Delta\delta       &= A\,X + F\,Y \\
\Delta\alpha^*     &= B\,X + G\,Y
\end{aligned}
\qquad\Longleftrightarrow\qquad
\begin{pmatrix} \Delta\delta \\ \Delta\alpha^* \end{pmatrix}
= \underbrace{\begin{pmatrix} A & F \\ B & G \end{pmatrix}}_{\mathbf{M}}
\begin{pmatrix} X \\ Y \end{pmatrix} .
$$

The TI elements are the four entries of a $2\times2$ matrix $\mathbf{M}$ which
maps the orbital plane onto the plane of the sky. Because the sky position is
*linear* in $A, B, F, G$ (once $P$, $e$, and $T_p$ are fixed), they are very
convenient for orbit fitting, whereas the angles $i$, $\Omega$ and $\omega$
appear non-linearly.

## Campbell $\rightarrow$ Thiele-Innes

In terms of the Campbell elements, the TI elements are (see Binnendijk 1960[^6];
Heintz 1978[^5], Halbwachs et al. 2023[^4]):

<!--  -->
$$
\begin{aligned}
A &= a (\cos \omega \cos \Omega - \sin \omega \sin \Omega \cos i) \\
B &= a (\cos \omega \sin \Omega + \sin \omega \cos \Omega \cos i) \\
F &= -a (\sin \omega \cos \Omega + \cos \omega \sin \Omega \cos i) \\
G &= -a (\sin \omega \sin \Omega - \cos \omega \cos \Omega \cos i)
\end{aligned}
$$

where the Campbell elements are defined as follows:


- $a$ (often called $a_0$ for a photocentric orbit) is the semi-major axis of
  the orbit. It is in the same units as the TI elements, often AU or mas.

- $i$ is the inclination of the orbit: the angle between the orbital plane and
  the plane of the sky. By convention, $i \in [0, \pi/2]$ when the orbital
  motion is in the direct sense (counterclockwise on the sky, i.e. in the
  direction of increasing position angle), and $i \in [\pi/2, \pi]$ when the
  motion is retrograde (clockwise).

- $\Omega$ is the position angle of the ascending node, measured from north
  through east. Without radial velocities there is no way to tell which of the
  two nodes is the ascending one, so the node taken as "ascending" is, by
  convention, the one on the side where right ascension increases. Therefore,
  $\Omega \in [0, \pi]$.  
  (When radial velocities are available the convention changes, see
  [below](#combining-with-radial-velocities).)

- $\omega$ is the argument of periastron, measured in the orbital plane starting
  from the ascending node defined above, in the sense of the orbital motion.  
  (Note that Halbwachs et al. call $\omega$ the "periastron longitude".)

### A geometric picture

To gain some intuition on the sines and cosines above, note that the
transformation from the orbital plane to the plane of the sky can also be
represented by the matrix product

$$
\mathbf{M} =
\begin{pmatrix} A & F \\ B & G \end{pmatrix}
= a \;
\underbrace{\begin{pmatrix} \cos\Omega & -\sin\Omega \\ \sin\Omega & \cos\Omega \end{pmatrix}}_{R(\Omega)}
\underbrace{\begin{pmatrix} 1 & 0 \\ 0 & \cos i \end{pmatrix}}_{\text{tilt}}
\underbrace{\begin{pmatrix} \cos\omega & -\sin\omega \\ \sin\omega & \cos\omega \end{pmatrix}}_{R(\omega)} .
$$

From right to left:

1. $R(\omega)$ rotates the periastron direction onto the line of nodes (the line
   where the orbital plane intersects the sky plane).
2. The tilt leaves the component *along* the line of nodes unchanged, and
   foreshortens the perpendicular component by $\cos i$.
3. $R(\Omega)$ rotates the line of nodes to its position angle on the sky.

In particular, a circle of radius $a$ in the orbital plane is mapped to an
ellipse on the sky with semi-axes $a$ and $a|\cos i|$.

## Thiele-Innes $\rightarrow$ Campbell

Any $2\times2$ matrix can be split in two pieces: one that acts as a rotation
combined with a scaling, and one that acts as a reflection combined with a
scaling. For $\mathbf{M}$, this split can be written

$$
\mathbf{M} =
\underbrace{\begin{pmatrix} c_1 & -s_1 \\ s_1 & c_1 \end{pmatrix}}_{\text{rotation} \times p}
+
\underbrace{\begin{pmatrix} c_2 & s_2 \\ s_2 & -c_2 \end{pmatrix}}_{\text{reflection} \times q}
\qquad\text{with}\qquad
\begin{aligned}
c_1 &= (A + G)/2, & s_1 &= (B - F)/2, \\
c_2 &= (A - G)/2, & s_2 &= (B + F)/2 .
\end{aligned}
$$

This is useful because the tilt matrix can also be split in the same way,

$$
\begin{pmatrix} 1 & 0 \\ 0 & \cos i \end{pmatrix}
= \frac{1 + \cos i}{2} \begin{pmatrix} 1 & 0 \\ 0 & 1 \end{pmatrix}
+ \frac{1 - \cos i}{2} \begin{pmatrix} 1 & 0 \\ 0 & -1 \end{pmatrix},
$$

and sandwiching each piece between $R(\Omega)$ and $R(\omega)$ gives a rotation
by $\omega + \Omega$ for the first piece, and a reflection involving $\omega -
\Omega$ for the second. Carrying this out, we find

$$
\begin{aligned}
A + G &= 2p \cos(\omega + \Omega), &\qquad B - F &= 2p \sin(\omega + \Omega), \\
A - G &= 2q \cos(\omega - \Omega), &\qquad B + F &= -2q \sin(\omega - \Omega),
\end{aligned}
$$

with
$$
p = a\,\frac{1 + \cos i}{2}, \quad q = a\,\frac{1 - \cos i}{2} .
$$

In summary:

- the sum $\omega + \Omega$ is the angle of the rotation part, 
- the difference $\omega - \Omega$ is the angle of the reflection part
- the sizes $p, q$ of the two parts carry $a$ and $i$.


### Semi-major axis and inclination

The sizes $p$ and $q$ can be read off from $\mathbf{M}$, and since $a = p + q$
and $\cos i = (p - q)/(p + q)$, we can obtain both $a$ and $i$. Binnendijk
(1960)[^6] arranges the calculation through the intermediate variables

$$
u = \frac{A^2 + B^2 + F^2 + G^2}{2}
\qquad \text{and} \qquad
v = AG - BF .
$$

These have a simple meaning: $u = p^2 + q^2$ and $v = p^2 - q^2 = a^2 \cos i$
(the determinant of $\mathbf{M}$, which is unchanged by the rotations and equals
$a^2\cos i$ because of the tilt). 

Then $(u + v)(u - v) = 4p^2q^2$, so that $u + \sqrt{(u+v)(u-v)} = (p+q)^2 =
a^2$, and

$$
\boxed{
  a = \sqrt{u + \sqrt{(u + v)(u - v)}}
}
\qquad\qquad
\boxed{
  i = \arccos \frac{v}{a^2}
}
$$

Note that the sign of $v$ (the orientation of the mapping) tells whether the
motion is direct ($v > 0$) or retrograde ($v < 0$), and the $\arccos$ returns $i
\in [0, \pi]$ as required.

!!! note "Numerical considerations"  
    The expression for $i$ is exact but ill-conditioned for nearly face-on orbits
    ($i \approx 0$ or $\pi$), because $\arccos$ is very flat near $\pm 1$ and
    rounding can even push $v/a^2$ outside $[-1, 1]$. Using $p$ and $q$ directly:

    $$
    \begin{aligned}
    p = \tfrac12\sqrt{(A+G)^2 + (B-F)^2} \qquad
      & a = p + q \\
    q = \tfrac12\sqrt{(A-G)^2 + (B+F)^2} \qquad
      & i = 2\arctan\sqrt{q/p}
    \end{aligned}
    $$

    avoids this. 
    Here, the expression for $i$ follows from 
    $\tan^2(i/2) = (1-\cos i)/(1+\cos i) = q/p$.  
    Halbwachs et al. (2023) use a similar half-angle formulation.

### The angles $\omega$ and $\Omega$

The angles are obtained from the two angles of the rotation and reflection
parts:

$$
\begin{aligned} 
\omega + \Omega &= \operatorname{atan2}\big(B - F,\; A + G\big) \\
\omega - \Omega &= \operatorname{atan2}\big(-(B + F),\; A - G\big)
\end{aligned}
$$

which are the angles of the vectors $(A+G,\, B-F) = 2p\,(\cos(\omega+\Omega),
\sin(\omega+\Omega))$ and $(A-G,\, -(B+F)) = 2q\,(\cos(\omega-\Omega),
\sin(\omega-\Omega))$. Since $p$ and $q$ are non-negative, the signs of the two
components fix the quadrant unambiguously.

!!! warning "Beware of the arctan and the sign"  
    
    Binnendijk's formulas are often written as 
    $\omega + \Omega = \arctan\frac{B-F}{A+G}$ and
    $\omega - \Omega = \arctan\frac{B+F}{G-A}$. These are correct as ratios (they
    equal the tangent of the angle), but a plain $\arctan$ discards the quadrant,
    so these are only *preliminary* estimates; the quadrant must then be fixed from
    the signs of the numerator and denominator (Halbwachs et al. 2023). In
    the second expression both $B+F$ and $G-A$ carry the opposite sign
    to $\sin(\omega-\Omega)$ and $\cos(\omega-\Omega)$ respectively, so naively
    passing them to `atan2` gives an angle that is off by $\pi$. Using the forms
    above, the signs are already absorbed.

The two angles are then recovered as

$$
\boxed{\omega = \frac{(\omega+\Omega) + (\omega-\Omega)}{2}}
\qquad\qquad
\boxed{\Omega = \frac{(\omega+\Omega) - (\omega-\Omega)}{2}}
$$

#### The $\pi$ ambiguity

The sum $\omega + \Omega$ and the difference $\omega - \Omega$ are only known
modulo $2\pi$, so $\omega$ and $\Omega$ are only known modulo $\pi$, and not
independently: shifting *both* by $\pi$,
$$
(\omega, \Omega) \;\longrightarrow\; (\omega + \pi,\; \Omega + \pi),
$$
leaves $A$, $B$, $F$ and $G$ unchanged (every term in their definitions is a
product of one function of $\omega$ and one of $\Omega$, and each flips sign).
Geometrically, this is the freedom of choosing *which end of the line of nodes*
is called the ascending node: swapping the two nodes changes $\Omega$ by $\pi$,
and since $\omega$ is measured from the ascending node, it changes by $\pi$ too.

The TI elements cannot resolve this degeneracy, so the convention $\Omega \in
[0,\pi]$ is used. In practical calculations: since `atan2` returns angles in
$(-\pi, \pi]$, the half-difference above lies in $(-\pi, \pi]$. If it is
negative, we add $\pi$ to **both** $\Omega$ and $\omega$. Finally, we wrap
$\omega$ into $[0, 2\pi)$ by adding or subtracting $2\pi$ as needed.

### Summary of the algorithm

1. Compute $p$, $q$ (or $u$, $v$) from $A, B, F, G$, then $a$ and $i$.
2. Compute $\omega + \Omega = \operatorname{atan2}(B - F, A + G)$ and
   $\omega - \Omega = \operatorname{atan2}(-(B + F), A - G)$.
3. Take the half-sum and half-difference to get $\omega$ and $\Omega$.
4. If $\Omega < 0$, add $\pi$ to both $\Omega$ and $\omega$; then wrap $\omega$ to $[0, 2\pi)$.

### Degenerate cases

- **Face-on orbits** ($i = 0$ or $\pi$): either $q$ or $p$ vanishes, so one of
  the two angles above is undefined (0/0). Only $\omega + \Omega$ (for $i=0$) or
  $\omega - \Omega$ (for $i=\pi$) is determined, and $\omega$ and $\Omega$
  individually are meaningless. For near face-on orbits, their uncertainties
  will be large.
- **Circular orbits** ($e \to 0$): the periastron is undefined, and so is
  $\omega$ (as well as $T_p$). This is a property of the orbit itself and is
  independent of the conversion, but it makes the TI elements and the derived
  $\omega$ poorly constrained.


## Combining with radial velocities

The $\Omega \in [0, \pi]$ convention above is a consequence of using astrometry
alone. In the radial-velocity convention, the ascending node is the one where
the star moves *away* from the observer, and this removes the ambiguity:
$\Omega$ can then take any value in $[0, 2\pi)$.

When a combined astrometric + RV solution is available, the astrometric
$(\omega, \Omega)$ and the spectroscopic $\omega_1$ must be reconciled by
choosing, between $(\omega, \Omega)$ and $(\omega + \pi, \Omega + \pi)$, the
pair that is consistent with $\omega_1$. Which one is correct also depends on
whether the photocentre follows the star whose velocities are measured (then
$\omega \approx \omega_1$) or its companion (then $\omega \approx \omega_1 \pm
\pi$), see Appendix B of Halbwachs et al. (2023). This is a piece of information
that neither dataset contains on its own.


## Orbital playground

The figure below lets you explore the conversion between Thiele-Innes and
Campbell elements, the degeneracy that arises with astrometry alone, and how
radial velocities resolve it.
<p class="print-only">
An interactive version is available at the kima documentation.
</p>

<iframe src="../../assets/thiele_innes_widget.html" class="no-print" 
        width="100%" height="1024" style="border:0" scrolling="no">
</iframe>
<img class="print-only" src="../../assets/ti_figure.png" alt="thiele_innes_widget">

## Implementation Notes

In **kima**, the conversions from Thiele-Innes to Campbell elements (and vice
versa) are implemented in two functions from `kima.pykima.analysis`:

- `kima.pykima.analysis.campbell_to_thiele_innes(a, i, Ω, ω)`
- `kima.pykima.analysis.thiele_innes_to_campbell(A, B, F, G, ω_ref=None)`

The latter function will resolve the `ω` ambiguity when a reference value is
provided, e.g., from a combined astrometry + RV fit.


## Acknowledgements

The author used Claude (Sonnet 5.5, Anthropic) to assist in the preparation of
this note: to review the mathematical derivations and the reference list, to
suggest the geometric interpretation of the inversion, to proofread the text,
and to write the interactive figure. The author checked the content and takes
responsibility for any remaining errors.


## References

[^1]: Thiele, T. N. *Neue Methode zur Berechung von Doppelsternbahnen*.
      AN 104, 245 (1883)  
      [NASA ADS](https://ui.adsabs.harvard.edu/abs/1883AN....104..245T){:target="_blank"}, 
      [DOI](https://doi.org/10.1002/asna.18831041503){:target="_blank"}

[^2]: Innes, R. *Orbital Elements of Binary Stars*.
      Circular of the Union Observatory Johannesburg, 68, 355 (1926)  
      [NASA ADS](https://ui.adsabs.harvard.edu/abs/1926CiUO...68..352V){:target="_blank"}  
      (note: this is the same reference as 3. below; Innes wrote Sections 2 and 4 of that article.)

[^3]: van den Bos. *Orbital Elements of Binary Stars*.
      Circular of the Union Observatory Johannesburg, 68, 352 (1926)  
      [NASA ADS](https://ui.adsabs.harvard.edu/abs/1926CiUO...68..352V){:target="_blank"}

[^4]: Halbwachs et al. *Gaia Data Release 3: Astrometric binary star processing*.
      A&A 674, A9 (2023)  
      [arXiv](https://arxiv.org/abs/2206.05726){:target="_blank"}, 
      [DOI](https://doi.org/10.1051/0004-6361/202243969){:target="_blank"}

[^5]: Heintz, W. D. *Double Stars*.
      Geophysics and Astrophysics Monographs (1978)  
      [NASA ADS](https://ui.adsabs.harvard.edu/abs/1978GAM....15.....H){:target="_blank"}

[^6]: Binnendijk, L. *Properties of Double Stars: A Survey of Parallaxes and Orbits*.
      University of Pennsylvania Press (1960)  
      [NASA ADS](https://ui.adsabs.harvard.edu/abs/1960pdss.book.....B/abstract)
      (ISBN10: 125844464X, ISBN 13:9781258444648)