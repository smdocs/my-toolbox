# Racket Language Notes

> General essence - Rules for function calls, if and cond expressions all work by trying to reduce program to a simpler program that does not have that construct
  - function call is replaced with body
  - if is replaced with true or false question
  - cond is replaced with one answer

#### Evaluation rules for a function call

For a call to a defined function such as (bulb (string-append "r" "ed")):

1. First reduce the operands to values (as for a call to a primitive). These values are called the arguments to the function.
2. Replace the function call expression with the body of the function in which every occurrence of the parameter(s) has been replaced by the corresponding argument(s).

For example:
```
(bulb (string-append "r" "ed"))
(bulb "red")
(circle 30 "solid" "red")
```
#### Evaluation rules of a <i>cond</i> expression

```racket
; if there are no question/answer pairs signal error. In this case we have question/ans pairs
; so proceed to the next eval rule
(cond [(> 1 2) "bigger"]
      [(= 1 2) "equal"]
      [(< 1 2) "smaller"])

;if the first question is true or value then replace the entire cond expression with the first answer.
;if the first question is not a value then evaluate it and replace it with its value.
; in this case the first question evaluates to false hence the question is replaced by false
(cond [false "bigger"]
      [(= 1 2) "equal"]
      [(< 1 2) "smaller"])

;if the first question is false then drop the entire question/ans pair from the cond expression
;therefore the above cond expression reduces to the one below.
(cond [(= 1 2) "equal"]
      [(< 1 2) "smaller"])

; Now we re-apply the rules to the newly formed cond expression
(cond [false "equal"]
      [(< 1 2) "smaller"])

;dropped the false uestion/answer pair
(cond [(< 1 2) "smaller"])

(cond [true "smaller"])

"smaller"
```
#### Evaluation rules for a <i>local</i> expression
- [x] Finish my changes
- [ ] Push my commits to github
- [ ] Open a pull request

@octocat :+1: This PR looks great - it's ready to merge! :shipit:

#### Evaluation rules for a <i>s-expression</i> or shared expression

#### References
1. [The why of Y](http://www.dreamsongs.com/Files/WhyOfY.pdf)
2. [The Structure of a Programming Language Revolution](http://www.dreamsongs.com/Files/Incommensurability.pdf)
3. [The art of Lisp](http://www.dreamsongs.com/ArtOfLisp.html)
4. [Racket Coding Guidelines](https://courses.edx.org/courses/course-v1:UBCx+SPD1x+1T2016/86a7af433b134f0a952b5614f5ad9a78/)
5. [Racket Style Guide](http://www.ccs.neu.edu/home/matthias/Style/style/Units_of_Code.html)
