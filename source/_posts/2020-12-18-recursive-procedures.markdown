---
layout: post
title: "Recursive Procedures"
date: 2020-12-18 12:16:44 +0000
comments: true
categories: 
---
           
Recursion is difficult to think about, but if you know two things, it becomes simple.

<!--more-->

## Recursive Procedures vs Recursive Processes
               
### Recursive Procedures

A recursive procedure is any procedure whose definition refers to itself (directly or indirectly).

They always have two components:

* A base case: the circumstances where routine does not call itself again, but instead returns a result. This can be thought of as the end, or the terminating conditions of the recursion.
* Rules for evolving the algorithm in such a way as to eventually converge on the base-case. This can be thought of as the filtering done in parent loop, to refine the work done in its ancestors. Or the logic that prevents the recursion from being infinite.

Recursive procedures are one of two _process_ types:

### Recursive Processes

A recursive _process_ is a type of recursive procedure characterised by a deferred operations chain (i.e. the procedure is written in such a way that each loop can not complete without first getting the result of the next iteration and then performing some further work on it - unless it’s the base case).

The execution of the recursive call goes through a period of expansion, where the process builds up a chain of deferred operations (by placing local variables and context on the call stack) and when the base-case is encountered, a period of contraction, where those scopes are popped off the call stack and executed.


It’s common to use a procedure for calculating the factorial of a number, as an example:
                                                                                          
```ruby
def factorial(number)
  return 1 if number <= 1

  number * factorial(number - 1)
end
```

The routine for `factorial(n)` cannot complete without first the routine for `factorial(n-1)` returning, to then be multiplied by `n`. The important bit is that we need to multiple by `n`, i.e. perform additional work with the result of a subsequent iteration; this means we need to keep a record of the value of `n` and the operation to be performed (a pointer to the routine) for each iteration (which is stored as part of the scope put on the call stack). 

So for any input value, during the expansion, a deferred operations chain is pushed onto the stack (one link for each recursive call), until we reach the base-case of `n == 1` (`n` is decremented with each call to the next loop, so we always get there eventually). Then, during contraction, each stage of that chain is popped off the call stack and executed with the value of `n` set at the time it was pushed onto the stack, and the result of the next loop (which holds the accumulative product of all the subsequent loops).

I prefer to think of these as "bottom-up" or "right-to-left" procedures, because when given a tree structure to operate on, in order to perform their work on each node, they must have the return value of the work done on their descendants, first.

### Iterative Processes

Iterative processes are a type of recursive procedure characterised by a finite set of state variables being passed between execution calls. This effectively means all context and the result of previous calls are passed as arguments to the next iteration (rather than storing future work to be done when subsequent loops return). Because of this, there is theoretically no need to keep the previous call’s scope on the stack and the routine can run in constant memory. However, in reality most languages still consume linear amounts of memory (in proportion to the number of recursive calls) and can still cause `StackOverflow` exceptions if the recursion is too deep, because they need to keep track of where to return to, after each loop is finished. 

To actually get iterative processes to run in constant memory, you have to take advantage of another their properties: they can be rewritten using looping constructs - such as `do`, `repeat`, `until`, `for` and `while` - likely with one or more variables in an outer scope that can be accessed from all iterations of the loop, which you can use to store the result of the previous loop. By rewriting them in terms of looping constructs, you allow the procedure to run in constant memory.

Some languages are smart enough to do this rewriting automatically for you at compile time or runtime, when the iterative process is _tail-recursive_, which means the call to itself is the final return value (i.e. its “tail” is a call to itself).

Rewriting the above example to be tail-recursive:

```ruby
def factorial(number, accumulator=1)
  return accumulator if number <= 1

  factorial(number - 1, number * accumulator)
end
```

This means passing an accumulator as an argument to hold the return value so the final line of the procedure is a call to itself (with no extra work to be performed). Now each iteration of the loop does all its work before passing onto the next iteration - there’s no need to record the value of any local variables or future operations (all context and the accumulator are passed on as arguments to the next iteration, instead).

I prefer to think of these as "top-down" or "left-to-right" procedures, because when given a tree structure to operate on, they perform their work for each parent node before calling themselves again for the descendant nodes.

### Summary

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 152px;"/>
        <col style="width: 298px;"/>
        <col style="width: 343px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 152px; padding: 8px;">
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 298px; padding: 8px;">
            <div>Description</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 343px; padding: 8px;">
            <div>Application</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 152px; padding: 8px;">
            <div>Recursive processes</div>
            <div><br/></div>
            <div>a.k.a Bottom-up (or right-to-left)</div>
            <div><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 298px; padding: 8px;">
            <div>Partially evaluating or deferring earlier (higher up) iterations and waiting for the result
                of later iterations to then customise or merge data with the higher evaluation
            </div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 343px; padding: 8px;">
            <div>When information on lower or subsequent iterations should affect how higher iteration should
                be performed.
            </div>
            <ul>
                <li>
                    Whether any ancestor meets requirements
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 152px; padding: 8px;">
            <div>Iterative processes</div>
            <div><br/></div>
            <div>a.k.a Top-down (or left-to-right)</div>
            <div><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 298px; padding: 8px;">
            <div>Evaluating earlier (higher up) algorithms and passing it through or down to later iterations
                as context to be used to customise (stopping condition) or merge data
            </div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 343px; padding: 8px;">
            <div>When information on higher iterations should affect how subsequent iterations should
                behave.
            </div>
            <ul>
                <li>
                    Flags/toggles
                </li>
                <li>
                    Max-depths/iterations
                </li>
            </ul>
        </td>
    </tr>
    </tbody>
</table>

## Message passing

We've discussed the ways that work can be sequenced and recursively evaluated, which we can think of as how the input is processed. But also of importance is how the results of that work is then collected, which we can think of as how the output is processed.

For simple cases, a single memo or total can be maintained (i.e. returned). However, for more complex cases, where the algorithm for collecting the results of each iteration involve one or mose decisions, a context or accumulator object may be required.


<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 152px;"/>
        <col style="width: 298px;"/>
        <col style="width: 343px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 152px; padding: 8px;">
            <div><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 298px; padding: 8px;">
            <div>Description</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 343px; padding: 8px;">
            <div>Application</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 152px; padding: 8px;">
            <div>Returning</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 298px; padding: 8px;">
            <div>Returning the results of subsequent operations and not mutating a shared cumulator</div>
            <ul>
                <li>
                    Don’t need to pass in a receiver of the answer
                </li>
            </ul>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 343px; padding: 8px;">
            <div>When there is only one way to cumulate the results</div>
            <ul>
                <li>
                    E.g. Totals, counts, flattening of arrays
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 152px; padding: 8px;">
            <div>Accumulator</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 298px; padding: 8px;">
            <div>Mutating a shared cumulator object instantiated in the first loop and passed in by a parent
                or 
            </div>
            <ul>
                <li>
                    <div>Requires breaking the algorithm into 2 parts: the actual recursive part, and a
                        wrapper that returns this cumulator
                    </div>
                </li>
            </ul>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 343px; padding: 8px;">
            <div>When subsequent iterations are best placed to decide how to integrate the results of their
                iteration into the cumulator
            </div>
            <ul>
                <li>
                    <div>When it’s non trivial to incorporate subsequent results</div>
                </li>
                <li>
                    <div>E.g. when you have several counters and merging them in an earlier iteration would
                        involve duplicating the logic in the subsequent one
                    </div>
                </li>
            </ul>
        </td>
    </tr>
    </tbody>
</table>

## Summary

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 93px;"/>
        <col style="width: 144px;"/>
        <col style="width: 256px;"/>
        <col style="width: 314px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 93px; padding: 8px;">
            <div><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 144px; padding: 8px;">
            <div><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 256px; padding: 8px;">
            <div>Returning</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 314px; padding: 8px;">
            <div>Accumulator</div>
        </td>
    </tr>
    <tr>
        <td rowspan="6"
            style="height: 200px; border: 1px solid rgb(204, 204, 204); width: 93px; padding: 8px;">
            <div>Iterative</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 144px; padding: 8px;">
            <div>Merging/calculation</div>
        </td>
        <td colspan="2" style="border: 1px solid rgb(204, 204, 204); width: 570px; padding: 8px;">
            <div>Done in child loop</div>
            <div><br/></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 144px; padding: 8px;">
            <div>Arguments</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 256px; padding: 8px;">
            <div>Remaining dataset (only)</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 314px; padding: 8px;">
            <div>Remaining dataset</div>
            <div>Accumulator (mutated by parent)</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 144px; padding: 8px;">
            <div>Side-effects</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 256px; padding: 8px;">
            <div>None</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 314px; padding: 8px;">
            <div>Mutates accumulator with value from current iteration + all parent iterations</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 144px; padding: 8px;">
            <div>Returns</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 256px; padding: 8px;">
            <div>Result of algorithm run on all parent loops + Result of algorithm run on all child loops
            </div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 314px; padding: 8px;">
            <div>Accumulator for convenience of outermost call</div>
            <div><br/></div>
            <div>Can split out into an outer call that does the setting of the accumulator and the returning
                for you
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 144px; padding: 8px;">
            <div>Initial value</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 256px; padding: 8px;">
            <div>Determined by first loop</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 314px; padding: 8px;">
            <div>Determined by first loop’s accumulator</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 144px; padding: 8px;">
            <div>Base case</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 256px; padding: 8px;">
            <div>Return final value </div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 314px; padding: 8px;">
            <div>Perform final mutation</div>
        </td>
    </tr>
    <tr>
        <td style="height: 200px; border: 1px solid rgb(204, 204, 204); width: 93px; padding: 8px;"
            rowspan="6">
            <div>Recursive</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 144px; padding: 8px;">
            <div>Merging/calculation</div>
        </td>
        <td colspan="2" style="border: 1px solid rgb(204, 204, 204); width: 570px; padding: 8px;">
            <div>Done in parent loop</div>
            <div><br/></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 144px; padding: 8px;">
            <div>Arguments</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 256px; padding: 8px;">
            <div>Remaining dataset </div>
            <ul>
                <li>
                    <div>Result of algorithm run on all parent loops (result so far)</div>
                </li>
            </ul>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 314px; padding: 8px;">
            <ul>
                <li>
                    <div>Remaining dataset</div>
                </li>
                <li>
                    <div>Accumulator (not yet mutate by parent)</div>
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 144px; padding: 8px;">
            <div>Side-effects</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 256px; padding: 8px;">
            <div>None</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 314px; padding: 8px;">
            <div>Mutates accumulator with value from current iteration + result from all child iterations
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 144px; padding: 8px;">
            <div>Returns</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 256px; padding: 8px;">
            <div>Result of algorithm of all child iterations</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 314px; padding: 8px;">
            <div>Accumulator for convenience of outermost call</div>
            <div><br/></div>
            <div>Can split out into an outer call that does the setting of the accumulator and the returning
                for you
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 144px; padding: 8px;">
            <div>Initial value</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 256px; padding: 8px;">
            <div>Determined by last (innermost loop)</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 314px; padding: 8px;">
            <div>Determined by first loop’s accumulator</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 144px; padding: 8px;">
            <div>Base case</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 256px; padding: 8px;">
            <div>Return initial value</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 314px; padding: 8px;">
            <div>Perform first mutation</div>
        </td>
    </tr>
    </tbody>
</table>

## Examples

### Bottom-up, returning

```ruby 
# @note Algorithm for accumulation is always a simple addition, so can use returning
def sum_bur(arr)
  # Checking initial arguments
  return 0 unless arr.any?

  # Reduction or further focusing on remaining dataset
  remaining_elements = arr[1..-1]

  # Checking for stopping conditions
  if remaining_elements.any?
    # Execution traverses full recursion path before sending the result back up the call stack
    arr[0] + sum_bur(remaining_elements)
  else
    # Handling of stopping conditions - code that is evaluated on the final expansion step before contraction
    arr[0]
  end
end
```
  
### Top-down, returning

```ruby
# @note Algorithm for accumulation is always a simple addition, so can use returning
def sum_tdr(arr, total = 0)
  # Checking initial arguments
  return 0 unless arr.any?

  # Reduction or further focusing on remaining dataset
  remaining_elements = arr[1..-1]

  # Result is calculated in earlier iterations and then passed down into subsequent ones
  next_total = total + arr[0]

  # Checking for stopping conditions
  if remaining_elements.any?
    sum_tdr(remaining_elements, next_total)
  else
    next_total
  end
end
```
  
### Bottom-up, accumulator

```ruby
# @note Algorithm for accumulation is not simple, so a accumulator is used
# @note Even though algorithm is bottom up, the passing of the initial value of the accumulator is still
#       top-down
def sum_bua(arr, context = { total: 0, missing: 0 })
  # Checking initial arguments
  return context unless arr.any?

  # Reduction or further focusing on remaining dataset
  remaining_elements = arr[1..-1]

  # Checking for stopping conditions
  if remaining_elements.any?
    # Execution traverses full recursion path before sending the result back up the call stack

    sum_bua(remaining_elements, context)
  else
    # Mutate/update accumulator rather than return a value
    if arr[0].nil?
      context[:missing] += 1
    else
      context[:total] += arr[0]
    end
  end

  # Context is returned by convenience for top-most call, to avoid having to specify the accumulator in the
  # outer context when calling the method
  context
end
```
  
### Top-down, accumulator

```ruby
# @note Algorithm for accumulation is not simple, so a accumulator is used
def sum_tda(arr, context = { total: 0, missing: 0 })
  # Checking initial arguments
  return 0 unless arr.any?

  # Reduction or further focusing on remaining dataset
  remaining_elements = arr[1..-1]

  # Mutate/update accumulator rather than return a value
  # @note This is done before calling the next iteration
  if arr[0].nil?
    context[:missing] += 1
  else
    context[:total] += arr[0]
  end

  # Checking for stopping conditions
  # The handling of a stopping condition is to just not call itself any more
  if remaining_elements.any?
    sum_tda(remaining_elements, context)
  end

  # Context is returned by convenience for top-most call, to avoid having to specify the accumulator in the
  # outer context when calling the method
  context
end
```
