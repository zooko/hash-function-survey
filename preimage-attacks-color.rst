.. -*- coding: utf-8-with-signature-unix; fill-column: 73; indent-tabs-mode: nil -*-

========================================================================================
the historical success of collision attacks does not imply a danger of pre-image attacks
========================================================================================

*plus: a history of attacks on secure hash functions*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

by Zooko Wilcox-O'Hearn, `LeastAuthority.com`_, 2015-11-11

.. _`LeastAuthority.com`: https://LeastAuthority.com

DISCLAIMER AND WARNING
======================

*This document is a draft of a work-in-progress, which is being hosted in a publicly visible location only in order to facilitate collaborative editing among its authors and contributors.  It is not intended to be published in its current form and it may have errors or omissions.*

Summary
=======

Most of the secure hash functions ever designed have turned out to be
vulnerable to collision attacks. This includes the widely-used secure
hash functions MD5 and SHA-1.

What about pre-image and second-pre-image attacks? Have practical hash
functions historically been vulnerable to those?

The bottom line is that if you rely on collision-resistance, you should
live in fear. Most of the hash functions ever designed have turned out to
be vulnerable to collisions. If you rely on (second-)pre-image
resistance, you can rest easy—almost any hash function is safe for you.

Preliminaries
=============

The input to a secure hash function is called the *pre-image* and the
output is called the *image*.

A hash function *collision* is two different inputs (pre-images) which
result in the same output. A hash function is *collision-resistant* if an
adversary can't find any collision.

A hash function is *pre-image resistant* if, given an output (image), an
adversary can't find any input (pre-image) which results in that output.

A hash function is *second-pre-image resistant* if, given *one*
pre-image, an adversary can't find any *other* pre-image which results in
the same image.

When collision attacks don't matter
===================================

There are cases where collision-resistance doesn't matter at all and what
you care about is second-pre-image resistance.

For such uses it would be harmless to be able to generate collisions, but
harmful to be able to generate pre-images or second-pre-images [*]_. For
this purpose the relevant question is not whether hash function designs
have historically been revealed to be vulnerable to collisions but
instead whether they've been revealed to be vulnerable to
(second-)pre-images.

hash-based digital signatures
-----------------------------

An example of this is the construction of hash-based digital
signatures. Hash-based digital signatures are secure (resistant to
forgery) as long as the hash function they are built on has
second-pre-image resistance, e.g. [0]_.

Such a hash-based digital signature would fail if its underlying hash
function failed at second-pre-image resistance, but this is the *only*
way that it could be broken—any attack which was able to forge digital
signatures against such a scheme would *have* to violate the
second-pre-image resistance of the underlying hash function.

One reason that hash-based digital signatures might be useful is that if
an attacker has a sufficiently large quantum computer, they could forge
digital signatures that rely on factorization or discrete log, such as
RSA, DSA, ECDSA, or Ed25519. There is no reason to think that such a
quantum computer would enable them to break secure hash functions,
however.

Another reason is that even if the attacker does *not* have a
sufficiently large quantum computer, but has a mathematical breakthrough
that allows them to exploit the asymmetric crypto technique (such as
factoring, discrete log, code-based crypto, etc.), then they would be
able exploit asymmetric-crypto-based digital signatures, but not
hash-based digital signatures.

What about in the other direction, though? Can't we imagine an attacker
who can break hash-based signatures but can't break
asymmetric-crypto-based signatures? No—there cannot be such an
attacker. Any attacker who can break hash-based signatures can also break
asymmetric-crypto-based signatures, because the latter rely on hash
functions in addition to relying on their asymmetric crypto primitives.

.. role:: y
.. role:: r
.. role:: g
.. role:: c
.. role:: o

.. _`Figure 0`:

*color key: is relying on this safe?*

:r:`unsafe`
   You can be exploited if you rely on this.

:g:`safe`
   There is no reason to believe that relying on this will make you
   vulnerable to exploitation.

*Figure 0: safety of digital signature algorithms*

+-----------------------------------------+-----------+------------------+--------------------------------+-----------------+----------------+
| digital signature type                  | today     | quantum computer | asymmetric crypto breakthrough | hash collisions | hash preimages |
+=========================================+===========+==================+================================+=================+================+
| preimage-resistant-hash-based (SPHINCS) | :g:`safe` | :g:`safe`        | :g:`safe`                      | :g:`safe`       | :r:`unsafe`    |
+-----------------------------------------+-----------+------------------+--------------------------------+-----------------+----------------+
| all other post-quantum                  | :g:`safe` | :g:`safe`        | :r:`unsafe`                    | :r:`unsafe`     | :r:`unsafe`    |
| (McEliece, NTRUsign,                    |           |                  |                                |                 |                |
| LWE, Ring-LWE,                          |           |                  |                                |                 |                |
| Lattice-based signatures,               |           |                  |                                |                 |                |
| code-based signatures,                  |           |                  |                                |                 |                |
| Rainbow,                                |           |                  |                                |                 |                |
| multivariate-quadratic,                 |           |                  |                                |                 |                |
| etc.)                                   |           |                  |                                |                 |                |
+-----------------------------------------+-----------+------------------+--------------------------------+-----------------+----------------+
| all others (RSA, DSA,                   | :g:`safe` | :r:`unsafe`      | :r:`unsafe`                    | :r:`unsafe`     | :r:`unsafe`    |
| ECDSA, Ed25519, etc.)                   |           |                  |                                |                 |                |
+-----------------------------------------+-----------+------------------+--------------------------------+-----------------+----------------+

message authentication codes
----------------------------

password hashing functions
--------------------------

Secure hash functions are often used as an 

Proof-of-Work functions
-----------------------

When collision attacks *do* matter
==================================

Be careful about this! The ability to generate collisions can be
surprisingly harmful to many systems. This is one of those subtleties of
cryptographic engineering which frequently trip up engineers who are not
cryptography experts. The famous “Internet Root Cert” attack [18]_ is an
example of engineers working at VeriSign incorrectly thinking that their
system was not threatened by collisions (in the absence of
second-pre-images).

`git`, which uses SHA-1, is like VeriSign's MD5 certificates in this
way—it is *believed* by its developers [50]_ that a mere collision attack
(not second-pre-image) against SHA-1 wouldn't make git users vulnerable
to malicious action, but there is no proof of this belief.

.. XXX rsync

In contrast to VeriSign and git, the cryptographic constructions
mentioned above typically come with proofs showing that the security of
the construction is guaranteed, assuming the security of some underlying
component. For example, the hash-based digital signature schemes in [0]_
come with a proof that *any possible* attack which couldn't generate
second-pre-images against the hash function couldn't achieve forgery
against the signature scheme.

Unless you have such a “security reduction” proof 




Results
=======

Here are the results of my search for all state-of-the-art attacks on
widely-studied hash functions.

*The bottom line is that no widely-studied hash function has ever
succumbed to a (second-)pre-image attack except for one.*

That single exception is the second-oldest secure hash function ever
designed, *Snefru*, which was designed in 1989 and 1990, and which turned
out to be vulnerable to differential cryptanalysis. Differential
cryptanalysis was discovered (by the open research community) in 1990.

No other widely-studied hash function has been shown to be vulnerable to
a practical (second-)pre-image attack. Furthermore, no other
widely-studied hash function has been shown to be vulnerable to a
(second-)pre-image attack that is more efficient than brute force, even
if we were to count attacks too expensive for anyone to actually
implement!

The history of (second-)pre-image attacks is therefore quite different
from the history of collision attacks. Most hash functions have been
proven vulnerable to collision attacks more efficient than brute force,
and even to collision attacks that could be implemented in practice.

History of attacks on hash functions
====================================

This is a timeline of the publication of hash functions and of
publication of weaknesses in hash functions.

I omit attacks on reduced-round or otherwise weakened variants of hash
functions (there are a lot of those). I omit attacks that have
unrealistic requirements, like attacks that require 2¹²⁸ precomputation
or require the messages to be 2⁵⁶ blocks long.

.. _`Figure 1`:

*color key: is relying on this safe?*

:r:`no`
   You can be exploited if you rely on this.

:y:`maybe`
   There are known attacks but they are probably too expensive to
   actually implement. If the attacks have been secretly improved, or if
   the attacker has more efficient computational resources than we think,
   then maybe you can be exploited if you rely on this.

:o:`maybe`
   There are no known attacks that are cheaper than brute force, but the
   hash output size is small enough that brute force might be feasible,
   so maybe you can be exploited if you rely on this.

:g:`yes`
   There is no known attack cheaper than brute force, and to pay for a
   brute force attack is far, far beyond the bounds of possibility for
   the forseeable future. There is no reason to believe that relying on
   this will make you vulnerable to exploitation.


.. csv-table:: Figure 1: Chronological view of collision attacks
   :widths: 12,5,5,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8
   :header: hash,bits        ,cpb , '89         , '90         , '91         , '92         , '93         , '94         , '95         , '96         , '97         , '98         , '99   , '00         , '01         ,'02         , '03         , '04        , '05         , '06         , '07         , '08         , '09   , '10        , '11   , '12         , '13         , '14

   MD2           , :o:` ` 128, 638, :y:` ` [21]_, :y:` `      , :y:` `      , :y:` `      , :y:` `      , :y:` `      , :y:` `      , :y:` `      , :y:` `      , :y:` `      , :y:` `, :y:` `      , :y:` `      , :y:` `      , :y:` `      , :y:` `     , :y:` `      , :y:` `      , :y:` `      , :y:` `      , :y:` `, :r:` ` [*]_, :r:` `, :r:` `      , :r:` `      , :r:` `
   Snefru-2      , :o:` ` 128,  \?,             , :y:` ` [3]_ , :r:` ` [19]_, :r:` `      , :r:` `      , :r:` `      , :r:` `      , :r:` `      , :r:` `      , :r:` `      , :r:` `, :r:` `      , :r:` `      , :r:` `      , :r:` `      , :r:` `     , :r:` `      , :r:` `      , :r:` `      , :r:` `      , :r:` `, :r:` `     , :r:` `, :r:` `      , :r:` `      , :r:` `
   MD4           , :o:` ` 128,   3,             , :y:` ` [22]_, :y:` `      , :y:` `      , :y:` `      , :y:` `      , :r:` ` [20]_, :r:` `      , :r:` `      , :r:` `      , :r:` `, :r:` `      , :r:` `      , :r:` `      , :r:` `      , :r:` `     , :r:` `      , :r:` `      , :r:` `      , :r:` `      , :r:` `, :r:` `     , :r:` `, :r:` `      , :r:` `      , :r:` `
   RIPEMD        , :o:` ` 128,  \?,             , :y:` ` [23]_, :y:` `      , :y:` `      , :y:` `      , :y:` `      , :y:` `      , :y:` `      , :y:` `      , :y:` `      , :y:` `, :y:` `      , :y:` `      , :y:` `      , :y:` `      , :r:` ` [7]_, :r:` `      , :r:` `      , :r:` `      , :r:` `      , :r:` `, :r:` `     , :r:` `, :r:` `      , :r:` `      , :r:` `
   MD5           , :o:` ` 128,   6,             ,             ,             , :y:` ` [24]_, :y:` `      , :y:` `      , :y:` `      , :y:` `      , :y:` `      , :y:` `      , :y:` `, :y:` `      , :y:` `      , :y:` `      , :y:` `      , :r:` ` [7]_, :r:` `      , :r:` `      , :r:` `      , :r:` `      , :r:` `, :r:` `     , :r:` `, :r:` `      , :r:` `      , :r:` `
   HAVAL-256-3   ,        256,  \?,             ,             ,             , :g:` ` [25]_, :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `      , :g:` `      , :g:` `      , :r:` ` [11]_, :r:` `     , :r:` `      , :r:` `      , :r:` `      , :r:` `      , :r:` `, :r:` `     , :r:` `, :r:` `      , :r:` `      , :r:` `
   SHA-0         , :o:` ` 160,  \?,             ,             ,             ,             , :g:` ` [26]_, :g:` `      , :y:` ` [*]_ , :y:` `      , :y:` `      , :y:` `      , :y:` `, :y:` `      , :y:` `      , :y:` `      , :y:` `      , :y:` `     , :y:` `      , :r:` ` [27]_, :r:` `      , :r:` `      , :r:` `, :r:` `     , :r:` `, :r:` `      , :r:` `      , :r:` `
   GOST          ,        256,  \?,             ,             ,             ,             ,             , :g:` ` [28]_, :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `     , :g:` `      , :g:` `      , :g:` `      , :y:` ` [14]_, :y:` `, :y:` `     , :y:` `, :y:` `      , :y:` `      , :y:` `
   SHA-1         , :o:` ` 160,   5,             ,             ,             ,             ,             ,             , :g:` ` [29]_, :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `     , :r:` ` [15]_, :r:` `      , :r:` `      , :r:` `      , :r:` `, :r:` `     , :r:` `, :r:` `      , :r:` ` [51]_, :r:` `
   RIPEMD-160    , :o:` ` 160,  14,             ,             ,             ,             ,             ,             ,             , :g:` ` [30]_, :g:` `      , :g:` `      , :g:` `, :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `     , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `, :o:` ` [*]_, :o:` `, :o:` `      , :o:` `      , :o:` `
   Tiger         ,        192,   7,             ,             ,             ,             ,             ,             ,             , :g:` ` [31]_, :g:` `      , :g:` `      , :g:` `, :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `     , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `      , :g:` `
   Panama        ,        512, 2.5,             ,             ,             ,             ,             ,             ,             ,             ,             , :g:` ` [33]_, :g:` `, :g:` `      , :g:` `      , :y:` ` [34]_, :y:` `      , :y:` `     , :y:` `      , :y:` `      , :r:` ` [35]_, :r:` `      , :r:` `, :r:` `     , :r:` `, :r:` `      , :r:` `      , :r:` `
   Whirlpool     ,        512,  35,             ,             ,             ,             ,             ,             ,             ,             ,             ,             ,       , :g:` ` [32]_, :g:` `      , :g:` `      , :g:` `      , :g:` `     , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `      , :g:` `
   SHA-256       ,        256,  13,             ,             ,             ,             ,             ,             ,             ,             ,             ,             ,       ,             , :g:` ` [37]_, :g:` `      , :g:` `      , :g:` `     , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `      , :g:` `
   RadioGatún    ,        256,  \?,             ,             ,             ,             ,             ,             ,             ,             ,             ,             ,       ,             ,             ,             ,             ,            ,             , :g:` ` [38]_, :g:` `      , :g:` `      , :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `      , :g:` `
   Skein         ,        256,   6,             ,             ,             ,             ,             ,             ,             ,             ,             ,             ,       ,             ,             ,             ,             ,            ,             ,             ,             , :g:` ` [39]_, :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `      , :g:` `
   Blake         ,        256,   8,             ,             ,             ,             ,             ,             ,             ,             ,             ,             ,       ,             ,             ,             ,             ,            ,             ,             ,             , :g:` ` [40]_, :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `      , :g:` `
   Grøstl        ,        256,  11,             ,             ,             ,             ,             ,             ,             ,             ,             ,             ,       ,             ,             ,             ,             ,            ,             ,             ,             , :g:` ` [41]_, :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `      , :g:` `
   Keccak (SHA-3),        256,  11,             ,             ,             ,             ,             ,             ,             ,             ,             ,             ,       ,             ,             ,             ,             ,            ,             ,             ,             , :g:` ` [42]_, :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `      , :g:` `
   JH            ,        256,  16,             ,             ,             ,             ,             ,             ,             ,             ,             ,             ,       ,             ,             ,             ,             ,            ,             ,             ,             , :g:` ` [43]_, :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `      , :g:` `
   BLAKE2        ,        256,   4,             ,             ,             ,             ,             ,             ,             ,             ,             ,             ,       ,             ,             ,             ,             ,            ,             ,             ,             ,             ,       ,            ,       , :g:` ` [44]_, :g:` `      , :g:` `

.. csv-table:: Figure 2: Chronological view of (second-)pre-image attacks
   :widths: 12,5,5,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8
   :header: hash ,bits       ,cpb , '89         , '90         , '91         , '92         , '93         , '94         , '95         , '96         , '97         , '98         , '99   , '00         , '01    ,'02         , '03         , '04        , '05         , '06         , '07         , '08         , '09   , '10        , '11   , '12         , '13   , '14

   MD2           , :c:` ` 128, 638, :g:` ` [21]_, :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `      , :g:` `, :g:` `      , :g:` `      , :g:` `     , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `, :g:` `
   Snefru-2      , :c:` ` 128,  \?,             , :g:` ` [3]_ , :r:` ` [19]_, :r:` `      , :r:` `      , :r:` `      , :r:` `      , :r:` `      , :r:` `      , :r:` `      , :r:` `, :r:` `      , :r:` `, :r:` `      , :r:` `      , :r:` `     , :r:` `      , :r:` `      , :r:` `      , :r:` `      , :r:` `, :r:` `     , :r:` `, :r:` `      , :r:` `, :r:` `
   MD4           , :c:` ` 128,   3,             , :g:` ` [22]_, :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `      , :g:` `, :g:` `      , :g:` `      , :g:` `     , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `, :g:` `
   RIPEMD        , :c:` ` 128,  \?,             , :g:` ` [23]_, :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `      , :g:` `, :g:` `      , :g:` `      , :g:` `     , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `, :g:` `
   MD5           , :c:` ` 128,   6,             ,             ,             , :g:` ` [24]_, :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `      , :g:` `, :g:` `      , :g:` `      , :g:` `     , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `, :g:` `
   HAVAL-256-3   ,        256,  \?,             ,             ,             , :g:` ` [25]_, :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `      , :g:` `, :g:` `      , :g:` `      , :g:` `     , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `, :g:` `
   SHA-0         , :c:` ` 160,  \?,             ,             ,             ,             , :g:` ` [26]_, :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `      , :g:` `, :g:` `      , :g:` `      , :g:` `     , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `, :g:` `
   GOST          ,        256,  \?,             ,             ,             ,             ,             , :g:` ` [28]_, :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `      , :g:` `, :g:` `      , :g:` `      , :g:` `     , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `, :g:` `
   SHA-1         , :c:` ` 160,   5,             ,             ,             ,             ,             ,             , :g:` ` [29]_, :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `      , :g:` `, :g:` `      , :g:` `      , :g:` `     , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `, :g:` `
   RIPEMD-160    , :c:` ` 160,  14,             ,             ,             ,             ,             ,             ,             , :g:` ` [30]_, :g:` `      , :g:` `      , :g:` `, :g:` `      , :g:` `, :g:` `      , :g:` `      , :g:` `     , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `, :g:` `
   Tiger         ,        192,   7,             ,             ,             ,             ,             ,             ,             , :g:` ` [31]_, :g:` `      , :g:` `      , :g:` `, :g:` `      , :g:` `, :g:` `      , :g:` `      , :g:` `     , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `, :g:` `
   Panama        ,        512, 2.5,             ,             ,             ,             ,             ,             ,             ,             ,             , :g:` ` [33]_, :g:` `, :g:` `      , :g:` `, :g:` `      , :g:` `      , :g:` `     , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `, :g:` `
   Whirlpool     ,        512,  35,             ,             ,             ,             ,             ,             ,             ,             ,             ,             ,       , :g:` ` [32]_, :g:` `, :g:` `      , :g:` `      , :g:` `     , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `, :g:` `
   SHA-256       ,        256,  13,             ,             ,             ,             ,             ,             ,             ,             ,             ,             ,       ,             ,       , :g:` ` [37]_, :g:` `      , :g:` `     , :g:` `      , :g:` `      , :g:` `      , :g:` `      , :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `, :g:` `
   RadioGatún    ,        256,  \?,             ,             ,             ,             ,             ,             ,             ,             ,             ,             ,       ,             ,       ,             ,             ,            ,             , :g:` ` [38]_, :g:` `      , :g:` `      , :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `, :g:` `
   Skein         ,        256,   6,             ,             ,             ,             ,             ,             ,             ,             ,             ,             ,       ,             ,       ,             ,             ,            ,             ,             ,             , :g:` ` [39]_, :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `, :g:` `
   Blake         ,        256,   8,             ,             ,             ,             ,             ,             ,             ,             ,             ,             ,       ,             ,       ,             ,             ,            ,             ,             ,             , :g:` ` [40]_, :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `, :g:` `
   Grøstl        ,        256,  11,             ,             ,             ,             ,             ,             ,             ,             ,             ,             ,       ,             ,       ,             ,             ,            ,             ,             ,             , :g:` ` [41]_, :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `, :g:` `
   Keccak (SHA-3),        256,  11,             ,             ,             ,             ,             ,             ,             ,             ,             ,             ,       ,             ,       ,             ,             ,            ,             ,             ,             , :g:` ` [42]_, :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `, :g:` `
   JH            ,        256,  16,             ,             ,             ,             ,             ,             ,             ,             ,             ,             ,       ,             ,       ,             ,             ,            ,             ,             ,             , :g:` ` [43]_, :g:` `, :g:` `     , :g:` `, :g:` `      , :g:` `, :g:` `
   BLAKE2        ,        256,   4,             ,             ,             ,             ,             ,             ,             ,             ,             ,             ,       ,             ,       ,             ,             ,            ,             ,             ,             ,             ,       ,            ,       , :g:` ` [44]_, :g:` `, :g:` `

I label an attack as cheaper than brute force only if the attack comp
times the attack mem is less than the cost of brute force search (see
[1]_).

If you are aware of any other papers which fit these criteria, or if you spot
an error in this document, please write to me: zooko@LeastAuthority.com.

*Figure 3: Survey of the best known attacks on secure hash functions*

+----------------------+------+------+-----+-----------------------------------+---------------------------------+
| hash                 | year | bits | cpb | collision attacks                 | (second-)preimage attacks       |
|                      |      |      |     +------------+------+-----+---------+------------+------+-----+-------+
|                      |      |      |     | safe?      | comp | mem | ref     | safe?      | comp | mem | ref   |
+======================+======+======+=====+============+======+=====+=========+============+======+=====+=======+
| MD2                  | 1989 |  128 | 638 | :y:`maybe` | 2⁶⁴  | 2⁰  | `[†]`_  | :g:`yes`   | 2⁷²  | 2⁷² | [2]_  |
+----------------------+------+------+-----+------------+------+-----+---------+------------+------+-----+-------+
| Snefru -2 [3]_       | 1990 |  128 |  \? | :r:`no`    | 2¹³  | 2⁰  | [4]_    | :r:`no`    | 2²⁵  | 2⁰  | [4]_  |
+----------------------+------+------+-----+------------+------+-----+---------+------------+------+-----+-------+
| MD4                  | 1990 |  128 |   3 | :r:`no`    | 2²   | 2⁰  | [6]_    | :g:`yes`   | 2⁹⁵  | 2³⁸ | [5]_  |
+----------------------+------+------+-----+------------+------+-----+---------+------------+------+-----+-------+
| RIPEMD               | 1990 |  128 |  \? | :r:`no`    | 2¹⁸  | 2⁰  | [36]_   | :g:`yes`   |      |     |       |
+----------------------+------+------+-----+------------+------+-----+---------+------------+------+-----+-------+
| MD5                  | 1992 |  128 |   6 | :r:`no`    | 2²⁴  | 2⁰  | [9]_    | :g:`yes`   | 2¹²³ | 2⁴⁸ | [8]_  |
+----------------------+------+------+-----+------------+------+-----+---------+------------+------+-----+-------+
| HAVAL-256-3 [25]_    | 1992 |  256 |  \? | :r:`no`    | 2²⁹  | 2⁰  | [11]_   | :g:`yes`   | 2²²⁵ | 2⁶⁸ | [10]_ |
+----------------------+------+------+-----+------------+------+-----+---------+------------+------+-----+-------+
| SHA-0                | 1993 |  160 |  \? | :r:`no`    | 2³⁴  | 2⁰  | [13]_   | :g:`yes`   | 2¹⁸⁹ | 2⁸  |       |
+----------------------+------+------+-----+------------+------+-----+---------+------------+------+-----+-------+
| GOST                 | 1994 |  256 |  \? | :y:`maybe` | 2¹⁰⁵ | 2⁰  | [14]_   | :g:`yes`   | 2¹⁹² | 2⁷⁰ | [14]_ |
+----------------------+------+------+-----+------------+------+-----+---------+------------+------+-----+-------+
| SHA-1                | 1995 |  160 | 4.8 | :r:`no`    | 2⁶⁹  | 2⁰  | [15]_   | :g:`yes`   |      |     |       |
+----------------------+------+------+-----+------------+------+-----+---------+------------+------+-----+-------+
| RIPEMD-160 [30]_     | 1996 |  160 |13.6 | :o:`maybe` | 2⁸⁰  | 2⁰  | `[§]`_  | :g:`yes`   |      |     |       |
+----------------------+------+------+-----+------------+------+-----+---------+------------+------+-----+-------+
| Tiger [31]_          | 1996 |  192 | 6.2 | :g:`yes`   |      |     |         | :g:`yes`   | 2¹⁸⁹ | 2⁸  | [16]_ |
+----------------------+------+------+-----+------------+------+-----+---------+------------+------+-----+-------+
| Panama [33]_         | 1998 |  512 | 2.5 | :r:`no`    | 2⁶   | 2⁰  | [17]_   | :g:`yes`   |      |     |       |
+----------------------+------+------+-----+------------+------+-----+---------+------------+------+-----+-------+
| Whirlpool [32]_      | 2000 |  512 |23.1 | :g:`yes`   |      |     |         | :g:`yes`   |      |     |       |
+----------------------+------+------+-----+------------+------+-----+---------+------------+------+-----+-------+
| SHA-256 [37]_ [52]_  | 2001 |  256 |13.0 | :g:`yes`   |      |     |         | :g:`yes`   |      |     |       |
+----------------------+------+------+-----+------------+------+-----+---------+------------+------+-----+-------+
| RadioGatún [38]_     | 2006 |  256 |  \? | :g:`yes`   |      |     |         | :g:`yes`   |      |     |       |
+----------------------+------+------+-----+------------+------+-----+---------+------------+------+-----+-------+
| Skein [39]_          | 2008 |  256 | 6.5 | :g:`yes`   |      |     |         | :g:`yes`   |      |     |       |
+----------------------+------+------+-----+------------+------+-----+---------+------------+------+-----+-------+
| Blake [40]_          | 2008 |  256 | 7.6 | :g:`yes`   |      |     |         | :g:`yes`   |      |     |       |
+----------------------+------+------+-----+------------+------+-----+---------+------------+------+-----+-------+
| Grøstl [41]_         | 2008 |  256 |10.2 | :g:`yes`   |      |     |         | :g:`yes`   |      |     |       |
+----------------------+------+------+-----+------------+------+-----+---------+------------+------+-----+-------+
| Keccak (SHA-3) [42]_ | 2008 |  256 |10.4 | :g:`yes`   |      |     |         | :g:`yes`   |      |     |       |
+----------------------+------+------+-----+------------+------+-----+---------+------------+------+-----+-------+
| JH [43]_             | 2008 |  256 |14.0 | :g:`yes`   |      |     |         | :g:`yes`   |      |     |       |
+----------------------+------+------+-----+------------+------+-----+---------+------------+------+-----+-------+
| BLAKE2 [44]_         | 2012 |  256 | 3.5 | :g:`yes`   |      |     |         | :g:`yes`   |      |     |       |
+----------------------+------+------+-----+------------+------+-----+---------+------------+------+-----+-------+

*legend:*:
   * *bit*: the number of bits of output
   * *cpb*: cycles per byte [*]
   * *comp*: approximate computation required for the attack
   * *mem*: approximate memory required for the attack

.. [*] Cycles per byte for Panama were taken from on ebash's
       amd64-h9ivy_, 4096-byte blocks, median measurement, except for
       Panama, which is not measured on ebash. XXX this sentence is fucked up
       For Panama, I measured it
       on my laptop (an Intel(R) Core(TM) i5-3427U, which is similar to
       the ebash h9ivy machine) with Crypto++ v5.6.2's implementation of
       Panama. I also measured MD5, SHA-1, SHA-256, SHA-512, SHA-3-256,
       SHA-3-512, Tiger, Whirlpool, and RIPEMD-160 on my machine and
       confirmed that their measurements on my machine were similar to
       the measurements posted from amd64-h9ivy_.

.. | Snefru-3 [3]_  |      |          |     | :r:`no`    | 2²⁹  | 2⁰  |       | :r:`no`    | 2⁵⁶  | 2⁰  |       |
.. +----------------+      |          +-----+------------+------+-----+       +------+-----+------+-----+       +
.. | Snefru-4 [3]_  |      |          |     | :r:`no`    | ≥2⁴⁵ | 2⁰  |       | :y:`maybe` | ≥2⁸⁸ | 2⁰  |       |
.. +----------------+------+----------+-----+------------+------+-----+-------+------------+------+-----+-------+
.. +----------------+      |          +-----+------------+------+-----+-------+------+-----+------+-----+-------+
.. | HAVAL-256-4    |      |          |     | :r:`no`    | 2³⁶  | 2⁰  | [12]_ | :g:`yes`   | 2²⁵⁴ | 2⁶⁸ |       |
.. +----------------+      |          +-----+------------+------+-----+-------+------+-----+------+-----+-------+
.. | HAVAL-256-5    |      |          |     | :y:`maybe` | 2¹²³ | 2⁰  |       | :g:`yes`   | 2²⁵⁵ | 2⁶⁸ |       |


.. _[†]:

.. [*] For MD2, I marked it as "maybe" safe in the collisions column up
       until 2010 and then marked is as "no". This is even though there
       are no known collision attacks on them better than brute
       force. This is because MD2's 128-bit output means the brute force
       attack takes only 2⁶⁴ comp and negligible memory to find a
       collision. To do that much comp has become feasible over the last
       few years. For example, in 2014 the Bitcoin mining network is
       doing it approximately every 10 minutes [45]_, [46]_!

.. [*] SHA-0 was considered unsafe beginning in 1995, not because of any
       published attack on it, nor because the 2⁸⁰ work factor for the
       brute force collision attack was feasible, but because the NSA had
       asserted that something was wrong with SHA-0 when they published
       SHA-1.

.. _[§]:

.. [*] RIPEMD-160's 160-bit output means it takes only 2⁸⁰ comp and
       negligible memory to find a collision. In my estimation this was
       safe until recently and is now “maybe” safe. See also [47]_ and
       Table 5.1 of [49]_.

.. XXX Hm, actually maybe 2⁸⁰ is now unsafe! https://twitter.com/josephbonneau/status/436362370785751040

Discussion
==========

The main result of this investigation is that “the historical success of
collision attacks does not imply a danger of pre-image attacks”.

Another interesting pattern that I perceive in these results is that 




Acknowledgments
===============

Thanks to Daira Hopwood, Andreas Hülsing, and Samuel Neves for comments on this note.


.. [0] http://eprint.iacr.org/2011/484 Buchmann-2011
.. [1] http://cr.yp.to/papers.html#bruteforce Bernstein-2005
.. [2] http://www.springerlink.com/content/qn746388035614r1/ Knudsen-2007
.. [3] http://www.springerlink.com/content/t10683l407363633/ Merkle-1990
.. [4] http://www.springerlink.com/content/208q118x13181g32/ Biham-2008
.. [5] http://eprint.iacr.org/2010/583 Zhong-2010
.. [6] http://www.springerlink.com/content/v6526284mu858v37/ Naito-2006
.. [7] http://eprint.iacr.org/2004/199 Wang-2004 “Collisions for Hash Functions MD4, MD5, HAVAL-128 and RIPEMD”
.. [8] http://www.springerlink.com/content/d7pm142n58853467/ Sasaki-2009
.. [9] http://marc-stevens.nl/research/papers/MTh%20Marc%20Stevens%20-%20On%20Collisions%20for%20MD5.pdf Stevens-2007
.. [10] http://www.springerlink.com/content/d382324nl16251pp/ Sasaki-2008
.. [11] http://academic.research.microsoft.com/Publication/676305/cryptanalysis-of-3pass-haval Van-Rompay-2003
.. [12] http://www.springerlink.com/content/0n9018738x721090/ Yu-2006
.. [13] http://www.springerlink.com/content/3810jp9730369045/ Manuel-2008
.. [14] http://www.cosic.esat.kuleuven.be/publications/article-2091.pdf Mendel-2008
.. [15] http://people.csail.mit.edu/yiqun/SHA1AttackProceedingVersion.pdf Wang-2005b “Finding Collisions in the Full SHA-1”
.. [16] http://eprint.iacr.org/2010/016 Guo-2010
.. [17] http://radiogatun.noekeon.org/panama/PanamaAttack.pdf Daemen-2007 “Producing Collisions for Panama, Instantaneously”
.. [18] http://www.win.tue.nl/hashclash/rogue-ca/ Sotirov-2009
.. [19] http://link.springer.com/chapter/10.1007%2F3-540-46766-1_11 Biham-1991
.. [20] http://repo.zenk-security.com/Cryptographie%20.%20Algorithmes%20.%20Steganographie/Cryptanalysis%20of%20MD4.pdf .. Dobbertin-1995
.. [21] https://tools.ietf.org/html/rfc1115
.. [22] https://tools.ietf.org/html/rfc1186
.. [23] http://books.google.com/books?id=9Zi0__jNRvEC&lpg=PA1&ots=NJoLlc8QRz&dq=%E2%80%9CIntegrity%20Primitives%20for%20Secure%20Information%20Systems.%20Final%20Report%20of%20RACE%20Integrity%20Primitives%20Evaluation%20(RIPE-RACE%201040)%2C%E2%80%9D&lr&pg=PA71#v=onepage&q=ripemd&f=false
.. [24] https://tools.ietf.org/html/rfc1321
.. [25] http://labs.calyptix.com/files/haval-paper.pdf Zheng-1992 “HAVAL – a one-way hashing algorithm with variable length of output”
.. [26] "FIPS PUB 180 / Federal Information Processing Standards Publication 180 / 1993 MAY 11"
.. [27] http://link.springer.com/chapter/10.1007%2F11426639_3 Biham-2005 “Collisions of SHA-0 and Reduced SHA-1”
.. [28] "GOST 34.11-94, Information Technology Cryptographic Data Security Hashing Function (1994) (in Russian)"
.. [29] http://itl.nist.gov/fipspubs/fip180-1.htm SHA-1
.. [30] http://link.springer.com/chapter/10.1007%2F3-540-60865-6_44 “RIPEMD-160: A Strengthened Version of RIPEMD”
.. [31] http://link.springer.com/chapter/10.1007/3-540-60865-6_46 Anderson-1996 “Tiger: A fast new hash function”
.. [32] http://cryptospecs.googlecode.com/svn/trunk/hash/specs/whirlpool.pdf Barreto-2000 “The WHIRLPOOL Hashing Function”
.. [33] http://link.springer.com/chapter/10.1007/3-540-69710-1_5 Daemen-1998 “Fast Hashing and Stream Encryption with Panama”
.. [34] http://www.cosic.esat.kuleuven.be/publications/article-81.pdf Rijmen-2002 “Producing Collisions for PANAMA”
.. [35] http://radiogatun.noekeon.org/panama/ Daemen-2007 “Producing Collisions for Panama, Instantaneously”
.. [36] http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.106.4759 Wang-2005a “Cryptanalysis of the hash functions MD4 and RIPEMD”
.. [37] http://csrc.nist.gov/publications/fips/fips180-2/fips180-2.pdf “FIPS Publication 180-2”
.. [38] http://radiogatun.noekeon.org/ Bertoni-2006 “The RadioGatún Hash Function Family”
.. [39] http://www.skein-hash.info/sites/default/files/skein1.3.pdf Ferguson-2008 “The Skein Hash Function Family”
.. [40] https://131002.net/blake/ Aumasson-2008 “SHA-3 proposal BLAKE”
.. [41] http://www.groestl.info/ Gauravaram-2008 “Grøstl – a SHA-3 candidate”
.. [42] http://keccak.noekeon.org/ Bertoni-2008 “The Keccak sponge function family”
.. [43] http://www3.ntu.edu.sg/home/wuhj/research/jh/ Wu-2008 “The Hash Function JH”
.. [44] https://blake2.net/ Aumasson-2012 “BLAKE2: simpler, smaller, fast as MD5”
.. [45] https://en.bitcoin.it/wiki/Difficulty
.. [46] http://bitcoin.sipa.be/
.. [47] http://www.keylength.com/en/3/
.. [49] http://www.ecrypt.eu.org/documents/D.SPA.20.pdf Smart-2012 “ECRYPT II Yearly Report on Algorithms and Keysizes (2011-2012)”
.. [50] http://www.mail-archive.com/cryptography@metzdowd.com/msg10800.html Linus Torvalds email 
.. [51] http://oai.cwi.nl/oai/asset/21208/21208B.pdf Stevens-2013 “New collision attacks on SHA-1 based on optimal joint local-collision analysis”
.. [52] https://www.google.com/patents/US6829355 SHA-2 patent filed 2001


.. .. _Leurent-2008: http://www.di.ens.fr/~leurent/files/MD4_FSE08.pdf
.. .. _SHA-3-Zoo: http://ehash.iaik.tugraz.at/wiki/The_SHA-3_Zoo
.. _amd64-h9ivy: http://bench.cr.yp.to/results-hash.html#amd64-h9ivy



:Author: Zooko Wilcox-O'Hearn
:Contact: zooko@LeastAuthority.com
:Affiliation: LeastAuthority.com
:Revision: 0.11.0
:Date: 2014-02-15
:License: `Creative Commons Attribution 4.0 International License`_

.. _Creative Commons Attribution 4.0 International License: http://creativecommons.org/licenses/by/4.0/deed.en_US


.. raw:: html

   <script type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.7.1/jquery.min.js"></script>
   <script>
     $(document).ready(function() {
       $('.r').parent().addClass('r-parent');
     });
     $(document).ready(function() {
       $('.y').parent().addClass('y-parent');
     });
     $(document).ready(function() {
       $('.g').parent().addClass('g-parent');
     });
     $(document).ready(function() {
       $('.c').parent().addClass('c-parent');
     });
     $(document).ready(function() {
       $('.o').parent().addClass('o-parent');
     });
   </script>

   <style>
      .r-parent {background-color:#FF2A2A;}
   </style>
   <style>
      .y-parent {background-color:#FFFF00;}
   </style>
   <style>
      .g-parent {background-color:#00FF00;}
   </style>
   <style>
      .o-parent {background-color:#FF6600;}
   </style>
   <style>
      .c-parent {background-color:transparent;}
   </style>




---- moved aside

Newer hash functions do not appear to be vulnerable to collision attacks,
but since they are newer, there has also been less time for cryptanalysts
to find flaws in them. (See `Figure 1`_, below.)

What about pre-image attacks or second pre-image attacks? Have hash
functions historically turned out to be vulnerable to those?

The answer is that except for “Snefru” (published in 1990), no secure
hash function has ever been shown to be vulnerable to (second-)pre-image
attacks.

**Therefore the historical success of collision attacks does not imply a danger of pre-image attacks.**


-------

A widely cited web page shows a graphical representation of the history
of various hash functions being broken.

.. figure:: valerieaurora.org-hash-crop-2.png
   :target: http://valerieaurora.org/hash.html
   :alt: Image from `http://valerieaurora.org/hash.html`_, downloaded 2014-02-14
   :align: right
   :width: 12cm

   Image from `http://valerieaurora.org/hash.html`_, downloaded 2014-02-14

   (Click to view original.)

.. _`http://valerieaurora.org/hash.html`: http://valerieaurora.org/hash.html

The advice on that web page is that if you are relying on your hash
function for collision-resistance, then you should be prepared to migrate
to a new hash function every few years.

One limitation of this analysis is that it considers only *collision
attacks*. There are some use cases where collision attacks don't matter,
and all that matters is *pre-image attacks*. What do we learn if we apply
this sort of analysis to the history of pre-image attacks?

