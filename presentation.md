Those annotations can have things other than typing?!
=====================================================

Mattijs Ugen

---

whoami
======

- data engineer @ Netherlands Forensic Institute, The Hague
- use a lot of Python in our day-to-day
  - see [Threat to Life: Preventing Planned Murders with Python](https://www.youtube.com/watch?v=rrckKR305TU) @ EP2023

---

> Annotating functions with typing information is commonplace nowadays. 
> Annotations have become synonymous with typing information, even though they could be just about anything you’d want. 
> Are there use cases for function annotations other than typing? Is that useful? 
> 
> Should you care? 
> 
> Should you stop using typing?

---

Should you stop using typing?
=============================

no!

if you like using typing, use it!

---

So what are we talking about?
=============================

~~~
@cache
def calculate_thing(argument: bytes, n: int = 1) -> float:
  ...
~~~

So, typing, right?
==================

![PEP 3107](images/pep-3107.png)

![typing module, introduced in Python 3.5](images/docs-module-typing.png)

---

Right, so what else…
====================

~~~
def H(b):
    ...

def schnorr(M, x):
    ...
    e = H(r + M)
    s = k + e * x
    return s, e
~~~

*example only, please don't assume this is correct*

---

~~~
def digest(data):
    ...

def schnorr(message, private_key):
    ...
    signature_right = digest(exponent_nonce + message)
    signature_left = nonce + signature_right * private_key
    return signature_left, signature_right
~~~

---

~~~
def digest(data: bytes) -> int:
    ...

def schnorr(message: bytes, private_key: int) -> tuple[bytes, bytes]:
    ...
    signature_right = digest(exponent_nonce + message)
    signature_left = nonce + signature_right * private_key
    return signature_left, signature_right
~~~

---

Translation into a different domain
===================================

~~~
def digest(data: bytes) -> 'H':
    ...

def schnorr(message: 'M', private_key: 'x') -> ('s', 'e'):
    ...
    signature_right: 'e' = digest(exponent_nonce + message)
    signature_left: 's' = nonce + signature_right * private_key
    return signature_left, signature_right
~~~

---

Translation into a different domain
===================================

- flake8 is a lot happier
- both math and python domains represented
- *annotations used as documentation*

---

Guarding function inputs
========================

- function bodies often contain input validation
- how about we just annotate what's valid?

~~~
def schnorr(message: binary(max_size=4096, multiple_of=16),
            private_key: number(domain='Z', max=q)):
    ...
~~~

---

Guarding function inputs
========================

- no magic 'hook' to *do* the validation
- the interpreter ignores annotations…
- if only python had a neat way to wrap a function…

---

Guarding function inputs
========================

~~~
def guard(function):
    arguments = get_annotations(function)

    def wrapped(*args, **kwargs):
        # match arguments to args and kwargs
        # validate the guards with the values
        result = function(*args, **kwargs)
        # validate result with return guard
        return result

    return wrapped
~~~

---

Guarding function inputs
========================

~~~
@guard  # decorator will wrap the function, providing our 'hook'
def schnorr(message: binary(max_size=4096, multiple_of=16),
            private_key: number(domain='Z', max=q)):
    ...
~~~

---

Takeaways
=========

- annotations are just python
- any expression will do
- not all of them will make sense
- but there's more than typing
- …
- if you want there to be
