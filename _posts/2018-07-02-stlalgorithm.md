STL provides a lot of algorithms and knowing all of them makes programming a lot easier. Here are some in-depth analyzation and explanation for each one of them

all example code would have this definition before to make itself shorter

```cpp
using namespace std;

#define everything(range) std::begin(range), std::end(range)

auto iseven = [](const auto& a){return ((a % 2) == 0)};

template <typename itr>
void printall(itr begin, itr end)
{
	for (; begin != end; ++begin)
		std::cout << *begin << " ";
	std::cout << '\n';
}
```

# Non-modifying sequence operations

---

Operations that don't modify the range

## std::all\_of

+ Checks all of the range with the given function, and returns true if all elements satisfy the condition

```cpp
int nums[10] = {2, 4, 6, 8, 10, 2, 4, 6, 8, 10};
cout << boolalpha
	<< all_of(everything(nums), iseven)
	<< '\n';
```

`true`

## std::any\_of

+ Checks all of the range with the given function, and returns true if any element satisfies the condition

```cpp
int nums[10] = {1, 3, 5, 7, 9, 1, 3, 5, 7, 8};
cout << boolalpha
	<< any_of(everything(nums), iseven)
	<< '\n';
```

`true`

## std::none\_of

+ Opposite of all\_of, returns true if no element satisfies the condition

```cpp
int nums[10] = {1, 3, 5, 7, 9, 1, 3, 5, 7, 9};
cout << boolalpha
	<< any_of(everything(nums), iseven)
	<< '\n';
```

`true`

## std::for\_each

+ replaced by for range loop in c++11, applies the function to each element in the range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
for_each(everything(nums), [](auto& i){i++});
for_each(everything(nums), [](const auto& i){cout << i << " ";});
cout << '\n'
```

`2 3 4 5 6 7 8 9 10 11`

Any modifications in line 2 directly affects the value in the range because it was taken as a reference

## std::for\_each\_n

+ replaced by for range loop in c++11, applies the function to each element in the array in the range provided

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
for_each_n(begin(nums), 5, [](auto& i){i++});
for_each_n(begin(nums), 10, [](const auto& i){cout << i << " ";});
cout << '\n'
```

`2 3 4 5 6 6 7 8 9 10`

i++ was only applied to the first five elements.

## std::count

+ Counts an element in the range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 5, 6, 7, 8, 9};
int numFives = count(everything(nums), 5);
cout << numFives << '\n';
```

`2`

## std::count\_if

+ Counts elements in the range that matches the evaluation function

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int numEvens = count_if(everything(nums), iseven);
cout << numEvens << '\n';
```

`5`

Lambdas could also check with external data

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int match[3] = {2, 3, 4};
int numMatch = count_if(everything(nums), [&](const auto& i){
	for (auto& j : match)
	{
		if (j == i)
			return true;
	}
	return false;
});
cout << numMatch << '\n';
```

`3`

Three elements in the range match an element in match. [&] stands for take in everything in the scope as reference, so the array match could be directly accessed

## std::mismatch

+ Returns the first mismatching pair of elements in two ranges

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int match[10] = {1, 2, 3, 4, 5, 5, 7, 8, 9, 10};
// structured binding in c++17. The function returns a std::pair<itr, itr>
auto [first, second] = mismatch(everything(nums), everything(match));
cout << *first
	<< " and "
	<< *second
	<< " at position: "
	<< distance(begin(nums), first)
	<< '\n';
```

`6 and 5 at position: 5`

The position where the two ranges have a mismatch, and the values of the position.

If `std::end(match)` isn't provided, it would be defaulted to `std::begin(match) + (std::end(nums) - std::begin(nums))`

An evaluation function could also be used in the function

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int match[10] = {1, 2, 4, 2, 3, 5, 7, 9, 9, 10};
int check[3] = {7, 8, 9};
auto [first, second] = mismatch(everything(nums), begin(match), [&](const auto& a, const auto& b){
	if (!(a == b))
	{
		for (auto& i : check)
		{
			if (a == i)
				return false;
		}
	}
	return true;
});
cout << *first
	<< " and "
	<< *second
	<< " at position: "
	<< distance(begin(nums), first)
	<< '\n';
```

`8 and 9 at position 7`

The predicate given means if a is not equal to b and if a is equal to an element in check, false would be returned, indicating a mismatch.

Note that `!(a == b)` is used instead of `(a != b)`, because `==` is more likely to be implemented than `!=`. It is safer to use commonly used operators.

## std::find

+ searches for an element equal to a value

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
auto itr = find(everything(nums), 5);
cout << distance(begin(nums), itr)
	<< " "
	<< *itr
	<< '\n';
```

`4 5`

distance gets the position of the element by measuring the distance between the iterator and the beginning of the range, and \*itr gets the value.

## std::find\_if

+ searches for an element where the predicate evaluates to true.

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
auto itr = begin(nums);
while (true)
{
	auto itr = find_if(everything(nums), iseven);
	if (itr == end(nums))
		break;
	cout << *itr++ << " ";
}
cout << '\n';
```

`2 4 6 8 10`

Itr is given the first even number in the range, the value it contains is printed afterward and it is incremented by one.

It repeats until find\_if returns an index higher than the length of the range.

## std::find\_if\_not

+ searches for an element where the predicate evaluates to false.

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
auto itr = begin(nums);
while (true)
{
	auto itr = find_if_not(everything(nums), iseven);
	if (itr == end(nums))
		break;
	cout << *itr++ << " ";
}
cout << '\n';
```

`1 3 5 7 9`

It is the same principle as find\_if, but the evaluation must be false.

## std::find\_end

+ searches for a sequence of elements in a range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int pattern[3] = {4, 5, 6};
auto itr = find_end(everything(nums), everything(pattern));
if (itr != end(nums))
	cout << distance(begin(nums), itr) << '\n';
else
	cout << "Not found" << '\n';
```

`3`

A predicate could also be passed to the function

```cpp
find_end(everything(array), everything(pattern), [](const auto& a, const auto& b){return (a.value == b.value);});
```

## std::find\_first\_of

+ searches the range for any element in the other range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int match[5] = {4, 5, 7, 9, 3};
auto itr = find_first_of(everything(nums), everything(match));
cout << distance(begin(nums), itr) << '\n';
```

`2`

A predicate could also be passed to the function

## std::adjacent\_find

+ searches the range for any adjacent elements

```cpp
int nums[10] = {1, 2, 3, 4, 5, 5, 6, 7, 8, 9};
auto itr = adjacent_find(everything(nums));
cout << distance(begin(nums), itr) << '\n';
```

`4`

In this algorithm, the use cases could be extended, because you can put a predicate into the function.

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
auto itr = adjacent_find(everything(nums), greater<int>());
if (itr == end(nums))
	cout << "The array is sorted\n";
else
	cout << "The array is not sorted\n";
```

`The array is sorted`

the `std::greater` function would be called every time it compares two adjacent elements

It would return the end if it nothing matches, which is equivalent to when the range is sorted from least to greatest.

```cpp
int nums[10] = {1, 1, 3, 4, 5, 5, 6, 7, 8, 9};
int match[5] = {3, 4, 5, 6, 7};
auto itr = adjacent_find(everything(nums), [&](const auto& a, const auto& b){
	if (a == b)
	{
		for (auto& i : match)
		{
			if (a == i)
				return true;
		}
	}
	return false;
});
cout << distance(begin(nums), itr) << '\n';
```

`4`

The result is 4 even though the 0th and 1st element are equivalent, because the predicate would check if the value is adjacent and it matches an element in match.

## std::search

+ Searches range to find the first place where the pattern matches

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int match[3] = {4, 5, 6};
auto itr = search(everything(nums), everything(match));
cout << distance(begin(nums), itr) << '\n';
```

`3`

A predicate could be passed into the function.

A searcher could also be used in the function (c++17 feature)

Searcher does the same job as search, but it utilizes different searching algorithms. It is currently in experimental.

There are three searchers in STL:

+ default\_searcher
+ boyer\_moore\_searcher
+ boyer\_moore\_horspool\_searcher

## std::search\_n

+ Searches the range for a sequence of an element with a given count

```cpp
int nums[10] = {1, 2, 3, 4, 5, 5, 5, 6, 7, 8};
auto itr = search_n(everything(nums), 3, 5);
cout << distance(begin(nums), itr) << '\n';
```

`4`

A predicate could be passed into the function.

# Modifying sequence operations

---

Operations that modify the range

## std::copy

+ Copy the elements in one range to another

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int result[10];
copy(everything(nums), begin(result));
printall(everything(nums));
```

`1 2 3 4 5 6 7 8 9 10`

Nums is copied into result.

Note that copy is stated to not support overlapping ranges, but the following code proves otherwise on my mac with clang++.

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
copy(begin(nums), end(nums) - 2, begin(nums) + 2);
printall(everything(nums));
int otherNums = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
memcpy(&otherNums[2], &otherNums[0], 8 * sizeof(int));
printall(everything(otherNums))l
```

`
1 2 1 2 3 4 5 6 7 8
1 2 1 2 1 2 1 2 1 2
`

## std::copy\_if

+ Copy the elements in one range that matches the predicate to another

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int result[10];
auto itr = copy_if(everything(nums), begin(result), iseven);
printall(begin(nums), itr);
```

`2 4 6 8 10`

The values in nums would only be copied if it is an even number.

## std::copy\_n

+ Copy n elements in one range to another

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int result[10];
copy_n(begin(nums), 10, begin(result));
printall(everything(result));
```

`1 2 3 4 5 6 7 8 9 10`

## std::copy\_backward

+ Copies from one range to another. The elements are copied in reverse order but their relative order is preserved

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
copy_backward(begin(nums), end(nums) - 5, end(nums));
printall(everything(result));
```

`1 2 3 4 5 1 2 3 4 5`

Note that the third parameter in the function takes the end instead of the start.

This function is generally better than `copy` for when the start of the output is after the beginning of the input.

## std::move

+ Moves elements from one range to another. Same as copy, but uses move semantics.

## std::move\_backward

+ Moves elements from one range to another. Same as copy\_backward, but uses move semantics.

## std::fill

+ Assigns an element to a range.

```cpp
int nums[10];
fill(everything(nums), 0);
printall(everything(nums));
```

`0 0 0 0 0 0 0 0 0 0 0`

## std::fill\_n

+ Assigns an element to a number of elements in a range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
fill_n(begin(nums), 5, 0);
printall(everything(nums));
```

`0 0 0 0 0 6 7 8 9 10`

## std::transform

+ applies a function to a range of elements, and put the result in another range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int result[10];
transform(everything(nums), begin(result), [](const auto& i){return i + 2;});
printall(everything(nums));
```

`3 4 5 6 7 8 9 10 11 12`

A predicate that adds two to the number was passed into the algorithm.

Another array could be provided

```cpp
int nums1[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int nums2[10] = {9, 8, 7, 6, 5, 4, 3, 2, 1, 0};
int result[10];
transform(everything(nums1), begin(nums2), begin(result),
		[](const auto& a, const auto& b){return a + b;});
printall(everything(result));
```

`10 10 10 10 10 10 10 10 10 10`

The function added the first two arrays together.

Note that the same starting iterator could also be provided as the output to edit in place.

## std::generate

+ assign the result of a function to a range

```cpp
int nums[10];
generate(everything(nums), [i = 0]()mutable{return i++});
printall(everything(nums));
```

`0 1 2 3 4 5 6 7 8 9`

A mutable lambda is provided that gives the sequence of numbers.

## std::generate\_n

+ assign the result of a function to a number of elements in a range.

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
generate_n(begin(nums), 5, [i = 0]()mutable{return i++});
printall(everything(nums));
```

`0 1 2 3 4 6 7 8 9 10`

The function only acted on the first five elements.

## std::remove

+ remove the item from the range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
auto itr = remove(everything(nums), 5);
printall(begin(nums), itr);
```

`1 2 3 4 6 7 8 9 10`

5 is deleted from the array.

Note that it doesn't change the size of the array.

## std::remove\_if

+ remove the item that matches the evaluation predicate

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
auto itr = remove_if(everything(nums), iseven);
printall(begin(nums), itr);
```

`1 3 5 7 9`

## std::remove\_copy

+ same as remove, but puts the output in a different range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int result[10];
auto itr = remove_copy(everything(nums), begin(result), 5);
printall(begin(nums), result);
```

`1 2 3 4 6 7 8 9 10`

## std::remove\_copy\_if

+ same as remove\_if, but puts the output in a different range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int result[10];
auto itr = remove_copy_if(everything(nums), begin(result), iseven);
printall(begin(nums), itr);
```

`1 3 5 7 9`

## std::replace

+ replaces an old value in a range to a new value

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
replace(everything(nums), 5, 10);
printall(everything(nums));
```

`1 2 3 4 10 6 7 8 9 10`

## std::replace\_if

+ replaces an element that matches a predicate to a new value

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
replace_if(everything(nums), iseven, -1);
printall(everything(nums));
```

`1 -1 3 -1 5 -1 7 -1 9 -1`

## std::replace\_copy

+ same as replace, but puts the output in a different range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int result[10];
replace(everything(nums), begin(result), 5, 10);
printall(everything(nums));
```

`1 2 3 4 10 6 7 8 9 10`

## std::replace\_copy\_if

+ same as replace\_if, but puts the output in a different range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int result[10];
replace_copy_if(everything(nums), begin(result), iseven, -1);
printall(everything(result));
```

`1 -1 3 -1 5 -1 7 -1 9 -1`

## std::swap

+ swaps two elements.

```cpp
int a = 10;
int b = 20;
swap(a, b);
cout << a << " " << b << '\n';
```

`20 10`

Before move semantics, it is generally recommended to implement your own swap function, but move semantics makes it trivial.

If you still want to implement swap, add a public function called `swap()` in the class.

## std::swap\_ranges

+ swap elements from one range to another

```cpp
int nums1[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int nums2[10] = {10, 9, 8, 7, 6, 5, 4, 3, 2, 1};
swap_ranges(everything(nums1), begin(nums2));
printall(everything(nums1));
printall(everything(nums2));
```

`
10 9 8 7 6 5 4 3 2 1
1 2 3 4 5 6 7 8 9 10
`

The content between the two ranges is swapped.

## std::iter\_swap

+ equivalent to `std::swap(*itr1, *itr2)`

## std::reverse

+ reverse the order of elements within a range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
reverse(everything(nums));
printall(everything(nums));
```

`10 9 8 7 6 5 4 3 2 1`

The content within nums is reversed.

## std::reverse\_copy

+ same as reverse, but put the result into another range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int result[10];
reverse_copy(everything(nums), begin(result));
printall(everything(result));
```

`10 9 8 7 6 5 4 3 2 1`

## std::rotate

+ swaps the content until the second iterator is at the beginning of the range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
rotate(begin(nums), begin(nums) + 2, end(nums));
printall(everything(nums));
```

`3 4 5 6 7 8 9 10 1 2`

The second iterator, which is at position 2, is at the beginning of the range after rotation.

Note that reverse iterators might be useful for this function

## std::rotate\_copy

+ same as rotate, but puts the output into another range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int result[10];
rotate_copy(begin(nums), begin(nums) + 2, end(nums), begin(result));
printall(everything(result));
```

`3 4 5 6 7 8 9 10 1 2`

## std::random\_shuffle

+ shuffles a range using a function. Deprecated, use std::shuffle instead.

This function uses std::rand, which is considered harmful. It is deprecated in c++14 and removed in c++17

## std::shuffle

+ shuffles a range, aimed to replace random\_shuffle

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
random_device rng;
mt19937 urng(rng());
shuffle(everything(nums), urng);
printall(everything(nums));
```

The code outputs the numbers in nums in a random order.

both std::random\_device and std::mt19937 is in the random STL library.

std::random\_device is a uniform random number generator that guarantees randomness at a cost of speed

std::mt19937 is a pseudo random number generator which performs at a higher speed

the pseudo number generator is initialized with a random number, so the output is going to be random enough

rng could also be directly provided into swap in place of urng.

more information [here](https://meetingcpp.com/blog/items/stdrandom_shuffle-is-deprecated.html)

## std::sample

+ grabs a sample of elements within a range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int result[5];
sample(everything(nums), begin(result), 5, mt19937{random_device{}()});
printall(everything(nums));
```

The code outputs a sample of the numbers in nums.

read std::shuffle for more information.

## std::unique

+ removes all but the first element in a consecutive group of elements within a range

```cpp
int nums[10] = {1, 1, 2, 3, 4, 5, 1, 1, 6, 7};
auto itr = unique(everything(nums));
printall(begin(nums), itr);
```

`1 2 3 4 5 1 6 7`

It is normally preferred to sort the range first so the same element only appears once in the range

A predicate could also be provided to check whether the two elements is equivalent

```cpp
int nums[10] = {1, 1, 2, 3, 4, 5, 1, 1, 6, 7};
auto itr = unique(everything(nums), [](const auto& a, const auto& b){return ((a == b) || (a + 1 == b));});
printall(begin(nums), itr);
```

`1 3 5 1 6`

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int result[10];
auto itr = unique_copy(everything(nums), begin(result),
						[](const auto& a, const auto& b){cout << a << " " << b << '\n';return a + 1 == b;});
printall(begin(result), itr);
```

`
1 2
1 3
3 4
3 5
5 6
5 7
7 8
7 9
9 10
1 3 5 7 9
`

Note how it compares the first result to others instead of the one right behind.

## std::unique\_copy

+ same as unique, but puts the output in another range

```cpp
int nums[10] = {1, 1, 2, 3, 4, 5, 1, 1, 6, 7};
int result[10];
auto itr = unique_copy(everything(nums), begin(result),
						[](const auto& a, const auto& b){return ((a == b) || (a + 1 == b));});
printall(begin(result), itr);
```

`1 3 5 1 6`

# Partitioning operations

---

Operations that involves partitioning

## std::is\_partitioned

+ whether the elements that satisfy the predicate appears behind the ones that don't

```cpp
int nums[10] = {2, 4, 6, 8, 10, 1, 3, 5, 7, 9};
if (is_partitioned(everything(nums), iseven))
	cout << "all even numbers are before odd numbers\n";
```

`all odd numbers are before even numbers`

The function would return false if even one element doesn't satisfy the predicate.

## std::partition

+ partition the range using the evaluation predicate provided

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
auto itr = partition(everything(nums), iseven);
cout << "Evens: ";
for (auto i = begin(nums); i != itr; ++i)
	cout << *i << " ";
cout << "\nOdds: ";
for (; itr != end(nums); ++itr)
	cout << *itr << " ";
cout << '\n';
```

`
Evens: 10 2 8 4 6
Odds: 5 7 3 9 1
`

Note how the function doesn't guarantee the range to be ordered.

## std::partition\_copy

+ same as partition, but puts the output into another range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int result[10];
auto itr = partition_copy(everything(nums), begin(result), iseven);
cout << "Evens: ";
for (auto i = begin(result); i != itr; ++i)
	cout << *i << " ";
cout << "\nOdds: ";
for (; itr != end(result); ++itr)
	cout << *itr << " ";
cout << '\n';
```

`
Evens: 10 2 8 4 6
Odds: 5 7 3 9 1
`

## std::stable\_partition

+ same as partition, but maintains the relative order

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
auto itr = stable_partition(everything(nums), iseven);
cout << "Evens: ";
for (auto i = begin(nums); i != itr; ++i)
	cout << *i << " ";
cout << "\nOdds: ";
for (; itr != end(nums); ++itr)
	cout << *itr << " ";
cout << '\n';
```

`
Evens: 2 4 6 8 10
Odds: 1 3 5 7 9
`

Note that this function is slower than std::partition

## std::partition\_point

+ gets the point where the predicate isn't satisfied

```cpp
int nums[10] = {2, 4, 6, 8, 10, 1, 3, 5, 7, 9};
auto itr = partition_point(everything(nums), iseven);
cout << distance(begin(nums), itr) << '\n';
```

`5`

Note how the position is at the start of the second range instead of the end of the first range.

# Sorting operations

---

Operations that is related to sorting

A comparison predicate could be provided to each of the functions below

## std::is\_sorted

+ checks whether the range is sorted

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
cout << boolalpha << is_sorted(everything(nums)) << '\n';
```

`true`

A compare predicate could also be provided

```cpp
int nums[10] = {10, 9, 8, 7, 6, 5, 4, 3, 2, 1};
cout << boolalpha << is_sorted(everything(nums), greater<int>()) << '\n';
```

`true`

The default function uses the `<` operator

## std::is\_sorted\_until

+ returns the last element where the range is sorted

```cpp
int nums[10] = {1, 2, 3, 4, 5, 1, 2, 3, 4, 5};
auto itr = is_sorted_until(everything(nums));
printall(begin(nums), itr);
```

`1 2 3 4 5`

## std::sort

+ sorts the range

```cpp
int nums[10] = {3, 2, 1, 6, 5, 4, 9, 8, 7, 10};
sort(everything(nums));
printall(everything(nums));
```

`1 2 3 4 5 6 7 8 9 10`

## std::partial\_sort

+ sorts a number of elements within a range

```cpp
int nums[10] = {3, 4, 5, 1, 2, 6, 7, 8, 10, 9};
partial_sort(begin(nums), begin(nums) + 3, end(nums));
printall(everything(nums));
```

`1 2 3 5 4 6 7 8 10 9`

This is faster than sort in most cases

## std::partial\_sort\_copy

+ sorts as much of the first range as the second range could hold, and output the result into the second range

```cpp
int nums[10] = {10, 9, 8, 7, 6, 5, 4, 3, 2, 1};
int result[5];
partial_sort_copy(everything(nums), everything(result));
printall(everything(result));
```

`1 2 3 4 5`

Note that if the output is larger than the input, an iterator would be provided that points to the end, and the elements after is not affected.

## std::stable\_sort

+ same as sort, but the order of equal elements is guaranteed to be preserved

## std::nth\_element

+ returns what the element would be at a position if the range is sorted

```cpp
int nums[10] = {10, 9, 8, 7, 6, 5, 4, 3, 2, 1};
nth_element(begin(nums), begin(nums) + 5, end(nums));
cout << nums[5] << '\n';
```

`6`

Note that every element lower would be placed before the middle, and every element higher would be placed after the middle

# Binary search operations

---

Operations related to binary search. Works only on sorted ranges.

A comparison predicate could be provided to each of the functions below

## std::lower\_bound

+ returns an iterator to an element that is greater than or equals to the value provided

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
auto itr = lower_bound(everything(nums), 4);
cout << distance(begin(nums), itr) << '\n';
```

`3`

The function returns the end if nothing is found

## std::upper\_bound

+ returns an iterator to an element that is greater than the value provided

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
auto itr = upper_bound(everything(nums), 4);
cout << distance(begin(nums), itr) << '\n';
```

`4`

The function returns the end if nothing is found

## std::binary\_search

+ returns whether the element provided exists in the range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
bool result = binary_search(everything(nums), 4);
cout << boolalpha << result << '\n';
```

`true`

Use equal\_range if you want the result of the search

## std::equal\_range

+ returns a range of matching elements

```cpp
int nums[10] = {1, 2, 3, 4, 4, 4, 5, 6, 7, 8};
auto [first, second] = equal_range(everything(nums), 4);
printall(fisrt, second);
```

`4 4 4`

Note that first would be end if nothing matches, and the function returns a pair of iterators.

# Set operations

---

Operations that operates on a set

A comparison predicate could be provided to all functions below

## std::merge

+ Merge two ranges together

```cpp
int nums1[5] = {1, 3, 5, 7, 9};
int nums2[5] = {2, 4, 6, 8, 10};
int result[10];
merge(everything(nums1), everything(nums2), begin(result));
printall(everything(result));
```

`1 2 3 4 5 6 7 8 9 10`

Note that the two ranges need to be sorted.

## std::inplace\_merge

+ Merge two ranges in place

```cpp
int nums[10] = {1, 3, 5, 7, 9, 2, 4, 6, 8, 10};
inplace_merge(begin(nums), begin(nums) + 5, end(nums));
printall(everything(nums));
```

`1 2 3 4 5 6 7 8 9 10`

The second parameter is the middle of the two ranges.

## std::includes

+ Checks whether the first range contains the second range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int comp[5] = {1, 3, 5, 7, 9};
cout << boolalpha << includes(everything(nums), everything(comp)) << '\n';
```

`true`

Note that the order doesn't matter

## std::set\_difference

+ Outputs the difference between the first and second range into another range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int comp[5] = {1, 3, 5, 7, 9};
int result[5];
set_difference(everything(nums), everything(comp), begin(result));
printall(everything(result))
```

`2 4 6 8 10`

## std::set\_intersection

+ Outputs the intersection between the first and second range into another range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int comp[5] = {1, 3, 5, 7, 9};
int result[5];
set_intersection(everything(nums), everything(comp), begin(result));
printall(everything(result));
```

`1 3 5 7 9`

## std::set\_symmetric\_difference

+ Same as set\_difference, but contains the difference between both ranges

## std::union

+ Outputs the union between the two ranges to another range, where the same element in both ranges only appears once

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int union[10] = {5, 6, 7, 8, 9, 10, 11, 12, 13, 14};
int result[14];
union(everything(nums), everything(union), begin(result));
printall(everything(nums));
```

`1 2 3 4 5 6 7 8 9 10 11 12 13 14`

# Heap operations

---

Operations related to [heap sort](https://en.wikipedia.org/wiki/Heapsort)

Comparison predicate could be provided to all functions below

## std::is\_heap

+ checks if the range is a valid heap

```cpp
int nums[10] = {10, 9, 7, 8, 5, 6, 3, 1, 4, 2};
cout << boolalpha << is_heap(everything(nums)) << '\n';
```

`true`

## std::is\_heap\_until

+ checks if the range is a valid heap, and return an iterator to the last position where the range is still a heap

## std::make\_heap

+ Orders a range so it becomes a heap

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
make_heap(everything(nums));
printall(everything(nums));
```

`10 9 7 8 5 6 3 1 4 2`

## std::push\_heap

+ Pushes the last element into the heap

```cpp
int nums[11] = {10, 9, 7, 8, 5, 6, 3, 1, 4, 2, 11};
push_heap(everything(nums));
printall(everything(nums));
```

`11 10 7 8 9 6 3 1 4 2 5`

## std::pop\_heap

+ swaps the first and last element, and makes the first and last-1 element a heap, effectively popping the first element

```cpp
int nums[10] = {10, 9, 7, 8, 5, 6, 3, 1, 4, 2};
pop_heap(everything(nums));
printall(everything(nums));
```

`9 8 7 4 5 6 3 1 2 10`

## std::sort\_heap

+ sorts a heap into a sorted range

```cpp
int nums[10] = {10, 9, 7, 8, 5, 6, 3, 1, 4, 2};
sort_heap(everything(nums));
printall(everything(nums));
```

`1 2 3 4 5 6 7 8 9 10`

# min/max operation

---

Operations related to minimum and maximum

A compare predicate could be provided to each function below

## std::max

+ returns the max of the two elements

```cpp
cout << max(1, 2) << '\n';
```

`2`

An initializer list could also be provided

## std::max\_element

+ returns the max of the elements in a range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
cout << max_element(everything(nums)) << '\n';
```

`10`

## std::min

+ returns the min of the two elements

```cpp
cout << min(1, 2) << '\n';
```

`1`

An initializer list could also be provided

## std::min\_element

+ returns the min of the elements in a range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
cout << min_element(everything(nums)) << '\n';
```

`1`

## std::minmax

+ returns a pair of the minimum and the maximum element

```cpp
auto [min, max] = std::minmax(1, 2);
cout << min << " " << max << '\n';
```

`1 2`

An initializer list could also be provided

Calling minmax is normally faster than min and max separately.

## std::minmax\_element

+ returns the min and max of the elements within a range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
auto [min, max] = minmax_element(everything(nums));
cout << min << " " << max << '\n';
```

`1 10`

## std::clamp

+ constraints the value within a range

```cpp
cout << clamp(7, 5, 10) << " "
	<< clamp(3, 5, 10) << " "
	<< clamp(12, 5, 10) << '\n';
```

`7 5 10`

The first parameter is the value, second and third is the min and max.

# Comparison operations

---

Operations related to comparison

A comparison predicate could be provided to all functions below

## std::equal

+ Returns true if elements of two ranges are equal, returns false if any element is not equivalent to the corresponding element in the other range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int match[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
bool equal = equal(everything(nums), begin(match));
cout << ((equal) ? "The arrays are equivalent" : "The array is not equivalent") << '\n';
```

`The arrays are equivalent`

If `std::end(match)` isn't provided, it would be defaulted to `std::begin(match) + (std::end(nums) - std::begin(nums))`

## std::lexicographical\_compare

+ compare the two ranges [lexicographically](https://en.wikipedia.org/wiki/Lexicographical_order)

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int match[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11};
bool result = lexicographical_compare(everything(nums), everything(match));
cout << boolalpha << result << '\n';
```

`true`

Returns true only if the first range is less than the second.

## std::compare\_3way (c++20)

+ if c++20 <=> is defined, return the result
+ if == and < is defined, return `std::strong_ordering::less`, `std::strong_ordering::equal`, and `std::strong_ordering::greater`
+ if == is defined, return `std::strong_equality::equal`, `std::string_equality::nonequal`

```cpp
cout << boolalpha << (compare_3way(1, 1) == strong_ordering::equal) << '\n';
```

`true`

## std::lexicographical\_compare\_3way (c++20)

+ combination of lexicographical\_compare and compare\_3way

# Permutation operations

---

Operations related to permutations.

Permutations are sorted lexicographically

A comparison predicate could be provided to all following functions

## std::is\_permutation

+ Returns true if a range is a permutation to another. Returns false if it is not

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int match[10] = {2, 4, 6, 8, 10, 1, 3, 5, 7, 9};
cout << boolalpha << is_permutation(everything(nums), begin(match)) << '\n';
```

`true`

If `std::end(match)` isn't provided, it would be defaulted to `std::begin(match) + (std::end(nums) - std::begin(nums))`

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int match[10] = {2, 4, 6, 8, 9, 1, 3, 5, 7, 9};
cout << boolalpha << is_permutation(everything(nums), begin(match)) << '\n';
```

`false`

No matter how you permutate match, it cannot be equivalent to nums

## std::next\_permutation

+ Returns the next permutation

```cpp
int nums[3] = {1, 2, 3};
do
{
	for (auto& i : nums)
		cout << i << " ";
	cout << '\n';
} while (next_permutation(everything(nums)));
```

`
1 2 3
1 3 2
2 1 3
2 3 1
3 1 2
3 2 1
`

Note that the range needs to be sorted

The function returns false if there are no more permutations

## std::prev\_permutation

+ Returns the previous permutation

```cpp
int nums[3] = {3, 2, 1};
do
{
	for (auto& i : nums)
		cout << i << " ";
	cout << '\n';
} while (prev_permutation(everything(nums)));
```

`
3 2 1
3 1 2
2 3 1
2 1 3
1 3 2
1 2 3
`

# Numeric operations

---

operations about numbers

## std::iota

+ fills a range with increments of the previous value

```cpp
int nums[10];
iota(everything(nums), 1);
printall(everything(nums));
```

`1 2 3 4 5 6 7 8 9 10`

the third parameter is the number to start from

## std::accumulate

+ Gets the sum of all the values within a range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
cout << accumulate(everything(nums), 0) << '\n';
```

`55`

This function is very useful, not only for getting the sum of a range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
cout << accumulate(everything(nums), 1, multiplies<int>());
```

`3628800`

the third parameter is the initial value, and the four parameter is a predicate provided

## std::inner\_product

+ Gets the sum of all the products within two ranges

```cpp
int nums1[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int nums2[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
cout << inner_product(everything(nums1), begin(nums2), 0) << '\n';
```

`385`

What this does is `accu = accu + (*itra * *itrb)`

Both the plus operator and multiply operator could be customized

```cpp
int nums1[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int nums2[10] = {0, 0, 0, 0, 0, 6, 7, 8, 0, 0};
cout << inner_product(everything(nums1), begin(nums2), 0, plus<int>(), equal_to<int>()) << '\n';
```

What this does is return the number of elements that is equivalent. The addition adds the result of the equal\_to operation to the accumulator.

## std::adjacent\_difference

+ Gets the difference of every pair, and return the result in another range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int result[10];
adjacent_difference(everything(nums), begin(result));
printall(everything(result));
```

`1 1 1 1 1 1 1 1 1 1`

What this does is `*out++ = (*(itr + 1) - *itr++)`

the subtraction operator could be customized

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int result[10];
adjacent_difference(everything(nums), begin(result), plus<int>());
printall(everything(result));
```

`1 3 5 7 9 11 13 15 17 19`

the result is the sum of every adjacent pair

## std::partial\_sum

+ Gets the partial sum of the elements in a range

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int result[10];
partial_sum(everything(nums), begin(result));
printall(everything(result));
```

`1 3 6 10 15 21 28 36 45 55`

What this does is `*out++ = (*itr + *(itr + 1) + *(itr + 2) ... )`

the addition operator could be customized

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int result[10];
partial_sum(everything(nums), begin(result), multiplies<int>());
printall(everything(result));
```

`1 2 6 24 120 720 5040 40320 362880 3628800`

## std::reduce

+ Similar to accumulate. Supposed to be faster, but not evident through testing. Doesn't guarantee order.

## std::exclusive\_scan

+ Similar to partial\_sum, but doesn't include the current value when scanning.

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
int result[10];
exclusive_scan(everything(nums), begin(result));
printall(everything(result));
```

`0 1 3 6 10 15 21 28 36 45`

## std::inclusive\_scan

+ Equivalent to partial\_sum

## std::transform\_reduce

+ Equivalent to calling transform and then reduce. the first operator is for reduce, and the second one is for transform.

```cpp
int nums[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
cout << transform_reduce(everything(nums), 0, plus<int>(), [](const auto& i){return i + 1;}) << '\n';
```

`65`

Could provide either one or two ranges to the function

## std::transform\_exclusive\_scan

+ Equivalent to transform\_reduce, but for exclusive\_scan

## std::transform\_inclusive\_scan

+ Equivalent to transform\_reduce, but for inclusive\_scan

# Operations on uninitialized memory

---

Operations related to uninitialized memory. Not recommended since it could be done by new and delete

## std::uninitialized\_copy

+ Same as copy, but use operator new instead of operator =

## std::uninitialized\_copy\_n

+ Same as copy\_n, but use operator new instead of operator =

## std::uninitialized\_fill

+ Same as fill, but use operator new instead of operator =

## std::uninitialized\_fill]\_n

+ Same as fill\_n, but use operator new instead of operator =

## std::uninitialized\_move

+ Same as move, but use operator new instead of move semantics

## std::uninitialized\_move\_n

+ Same as move\_n, but use operator new instead of move semantics

## std::uninitialized\_default\_construct

+ Constructs a range into an uninitialized range

```cpp
vector<object> v{init, init, init, init};
vector<object> result(4);
uninitialized_default_construct(everything(v), begin(result));
```

## std::uninitialized\_default\_construct\_n

+ Constructs n number of elements in an uninitialized range

## std::uninitialized\_value\_construct

+ Same as uninitialized\_default\_construct, but with [value-initialization](http://en.cppreference.com/w/cpp/language/value_initialization)

## std::uninitialized\_value\_construct\_n

+ Same as uninitialized\_value\_construct, but with n elements

## std::destroy\_at

+ Destroys an element by calling its destructor

```cpp
int* nums = new int[10];
destroy_at(nums);
```

## std::destroy

+ Destroys a range of elements

```cpp
int* nums[10];
for (auto& i : nums)
	i = new int[10];
destroy(everything(nums));
```

## std::destroy\_n

+ Destroys n numbers of elements in a range
