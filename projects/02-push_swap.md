# 02 - Push_Swap
## The Project
Build a program to sort a list of numbers using two stack-like structures with specific operations, optimising the number of stack-altering operations.

The available operations:
- ``SA``, ``SB`` = Swap the positions of the top two elements of the specified stack.
- ``PA``, ``PB`` = Push the top item from A to B, or visa-versa
- ``RA``, ``RB`` = Rotate the specified stack so that the top element becomes the last element.
- ``RRA``, ``RRB`` = Reverse rotate the specified stack so that the last element becomes the top element.
- ``RR``, ``RRR`` = Apply the (reverse) rotate operation to both stacks as one operation.
- ``SS`` = Apply the swap operation to both stacks as one operation.
## What I learned
This was an interesting project to tackle. While ones first inclination is to go read up on every sort of sorting algorithm on wikipedia, there's only so far that takes you - with a non-standard set of operations, without performing mathematical analysis that we're not equipped to undertake, this project necessitated taking approaches from exisiting sorting algorithms, adapting them to setup provided, and experimenting.

First thing I did with the project was built a set of functions to model the working of these stacks. I envisioned these as circular lists, like the individual rotating discs of a combination padlock. It seemed natural then to model the stacks using a circular doubly-linked list, using a separate struct to track the current head of the circular list. This made building the functions for rotating the stacks quite a simple process.

```c
static void	reverse_rotate(t_stack *stack)
{
	if (!stack || !(stack->head))
		return ;
	stack->head = stack->head->previous;
}
```

Once that was built, and the input handling with it, I could focus on the sorting algorithm. The first thing I built as an insertion sort - I pushed everything into stack B, and then inserted it into the correct position. This didn't even get close to meeting any of the required performance metrics for the project. 

My next attempt was to leap into an attempt to do some sort of optimal choice algorithm, but I abandoned this as my code was ballooning and felt incredibly unstable. Reading further, I decided I liked the idea of a Radix sort, and set about implementing that, opting for a Least Significant Digit approach. To do this, I ran a normal sorting algorithm over the list of numbers to identify what the final position of each number in the list needed to be, and then sorted based on the binary representation of those position numbers, using the two stacks as my two bins for radix. This ended up being really quick to implement with only a few short functions.
```c
void	sort_radix(t_stack *stack_a)
{
	const int	bit_max = get_bit_size(stack_a->size);
	int			index;

	index = 0;
	while (index < bit_max && !is_sorted(stack_a))
		push_matching_bit(stack_a, index++);
}
```
My initial implementation didn't quite meet the minimum performance benchmarks, by analysing the movements of the algorithm I found that which stack to make the 0 and 1 bins made a big difference in efficiency, getting me over the minimum performance benchmarks. Further optimisation work lead to finding that on the last iteration a number of operations could be removed by finishing one of the steps early, resulting in performance that was close to hitting the highest performance benchmarks.



While I settled for this initally, I eventually returned to the project, keen to do better. This time I returned to trying an optimal choice algorithm, implementing what is essentially a greedy algorithm - at every step, looking through all unsorted numbers, and identifying which number would take the least operations to insert into the correct position in the sorted list. The implementation went a lot smoother this time, and mostly cleared the max performance targets - it cleared it in the average case at least. What finally got me over the line was applying a quick presort - using a single pass of a MSD radix sort, the unsorted list would get sorted so that the top half of the numbers would be in one half of the unsorted list, and the bottom half with be in the other, so that generally numbers of a similar size should be closer to each other. This did the trick, clearing the max performance targets by a wide margin.

```c
void	sort_greedy(t_stack *stack_a, t_stack *stack_b)
{
	t_choice	best;

	greedy_init(stack_a, stack_b);
	while (stack_b->head)
	{
		best = greedy_search(stack_a, stack_b);
		greedy_insert(stack_a, stack_b, &best);
	}
	shift_min_to_top(stack_a);
}
```