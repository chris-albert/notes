
# Category Theory

### Category (1.2)
- Identity
- Composition
- Associative

### Functions and Epimorphims (2.1)
- Domain
  - Set of input values for with the function is defined
- Codomain
  - Target set of a function which all output is constraind to fall
- Image
  - Subset of codomain which is the output of a function

- Surjective (Onto) (Epimorphism) (Epic)
  - Given `f :: a -> b`, event element `b` in the codomain of `f`, there is at least one element `a` in the domain of `f`
    such that `f(x) = y`.
- Injective (One-to-One) (Monomorphism) (Monic)
  - Preserved distictness. It never maps distinct element of its domain to the same element of its codomain.
- Bijective (Isomorphism)
  - Both Surjective and Injective
  
- If `f . h` and `f . h'` are the same than its a monomorphism
- If `g . f` and `g' . f` are the same than its a epimorhphism
```
   h        g
 /--\  f  /--\
z    a-->b    c
 \--/     \--/
  h'       g'
```
### Categories, orders, monoids (3.1)

- Orders 
  - Category in which arrows are *not* functions
  - Arrows are releations.... like <= or >=
    - Total order 
      - Normal sorting
      - Between any objects there is an arrow (order)
    - Pre order (Thin category)
      - Satisfies minimum requirements (Composition)
      - if `a <= b` and `b <= c` then `a <= c`
      - There is not nessisarily an arrow between any 2 objects
    - Partial order
      - DAG
      - No arrow going backwards
    
- Hom-Set
  - C (a, b) or C (a, a)
  - Set of arrows
