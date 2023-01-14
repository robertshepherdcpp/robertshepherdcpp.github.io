Hello Welcome To My Website!

This is where you can find stuff about me and read my blogs!

---------
### About Me!
---------

So I am Robert Shepherd, I am a C++ Developer, I am self-taught! ***Many Many Books have been read to get me this far!***. I have quite a few GitHub repositories like the pse library, format library, Decomp etc. Here is a table of them and what I do with them!

Repositories | How often i contribute | My current issues with them  | Link
------------ | ------------- | ------------- | -------------------
👩‍💻 pse | ✅ At least 5 commits a day | ⭕️ Lots of the files have bugs | 🔗 https://github.com/robertshepherdcpp/pse
👩‍💻 files | ✅ I dont really add anything more to them | ⭕️ Not really many issues as they are only small programs | 🔗 https://github.com/robertshepherdcpp/files
👩‍💻 SerenityOS | ✅ I try to get familiar with the codebase at least every other day | ⭕️ It is a big codebase in SerenityOS! | 🔗 https://github.com/SerenityOS/serenity
👩‍💻 Decomp | ✅ I don't contribute to it any more | ⭕️ It wasn't really much of a repository and it is very error prone! | 🔗 https://github.com/robertshepherdcpp/Decomp
👩‍💻 format | ✅ I don't contribute to it any more | ⭕️ I have had it a while and I just thought I would put it on github, it doesn't need anything more added to it as it is just supposed to be a really basic tool. | 🔗 https://github.com/robertshepherdcpp/format

----------------------------
### Cool Decreasing Tuple Idiom!
----------------------------

So recently, I have found a super cool idiom! It is where you can make a tuple shrink, well a tuple of the same types because you cannot have varying return types! So you can just call a member function `remove_first` or anything you want to call it when you are implementing it, and then it shrinks the tuple by one element. It is a simple yet cool way of shrinking a tuple. It's implementation looks something like this:

```C++
template<typename T, typename... Ts>
struct Tuple
{
  T first;
  Tuple<Ts...> second{};
  bool first_removed = false;
  
  auto remove_first()
  {
    if(first_removed == false)
    {
      first_removed = true;
    }
    else
    {
      second.remove_first();
    }
  }
  
  template<auto T>
  auto get()
  {
    if constexpr(T == 0 && first_removed == false)
    {
      return first;
    }
    else if constexpr(T == 0 && first_removed == true)
    {
      return second.get<T>();
    }
    else
    {
      return second.get<T - 1>();
    }
  }
};

template<typename T>
struct Tuple<T>
{
  T first;
  bool first_removed = false;
  
  auto remove_first()
  {
    first_removed = true;
  }
  
  template<auto T>
  auto get()
  {
    if(T == 0 && first_removed == false)
    {
      return first;
    }
    else
    {
      // can't do anything, the user has entered in wrong input.
    }
  }
};
```

So the main part of it is that we have a variable first_removed and we set that when we want to remove the first element. We don't really remove the first element: first, we just make it look like we have removed the first element. This is because say we had a scenario like this:

```C++
auto x = Tuple<int, bool, double, float> x{/*initaialize it*/};
// now we want to change the size of the tuple.

x = Tuple<int, bool, double>{/*Initialize*/};
// that would result in an error
```
So instead we have to make the Tuple look like it is shorter than before but it really isn't because we cant have a type of Tuple<int, double, bool, char> and then just change it to Tuple<double, bool, char> because that if not a valid type conversion.


So instead we make it look like the elements have gone but they really haven't. We could go so far in implementing a function that is called get_front_element whose implementation looks something like this:

```C++
auto get_front_element()
{
  if(first_removed == true)
  {
    first_removed = false;
  }
  else
  {
    second.get_front_element();
  }
}
```
So that uses a cool recursion tequnique in order to free up the element the we got rid of, but we didn't actually.


So inconclusion, this is such a simple trick but it is so cool! By the way if you have any feedback please give it to me as this is my first blog!

Mlib Features
----------------------------------------------------------------------------------------------------
`mlib::amount`, is a new feature that will allow code like this, [godbolt](https://www.godbolt.org/z/enMhPhv67):
```C++
int main()
{
    std::string s{};
    mlib::amount_t<10>.times([&](){s += "a";});
    std::cout << s << "\n"; // outputs: aaaaaaaaaa
}
```
This will be very useful and it is all happening at compile time, as after all this is a metaprogramming library! You could also do it with `constexpr_while` but this method is more clean and concise.

Here is the implementation, it doesn't need to use the `std`, it is relatively quick, but it does use recursion, so can be really slow when there is `500` recursions. To actually do the recursion you need to use the `.times(auto&& lambda)` mmber function. So here is the implementation:
```C++
namespace mlib
{
    template<auto T>
    struct amount
    {
        constexpr auto times(auto&& lambda) const;
    };

    template<auto T>
    static constexpr auto amount_t = amount<T>{};

    template<auto T>
    constexpr auto amount<T>::times(auto&& lambda) const
    {
        lambda();
        if constexpr(T - 1 != 0)
        {
            amount_t<T - 1>.times(lambda);
        }
        return true;
    }
}; // namespace mlib
```
An easy way to use it, as shown in the first code snippet is just to use:
```C++
amount_t<NUM_OF_RECURSIONS>.times([](){});
```
----------------------------------------------------------------------------------------------
`mlib::get_nth_element` will be a new feature and will allow code like this: [godbolt](https://www.godbolt.org/z/zadoz8fcz):
```C++
int main()
{
  mlib::get_nth_element<N>(42, 'c', 3.14, true); // will return the Nth element of the args passed in.

mlib::get_nth_element<2>(42, 'c', 3.14, true); // returns 3.14
}
```
This will be useful because 1. It is very fast, is doesn't use recursion it instead just uses the tequnice [Imeadiatley Invoked Lambdas](https://dev.to/pratikparvati/lambda-functions-in-c-39dh#:~:text=An%20immediately%20invoked%20function%20expression%20%28IIFE%29%20is%20a,and%20executing%20code%20without%20polluting%20the%20global%20namespace.). It is relatively simple, and it very easy to use pass the element you want to find, `N` in a template parameter: `mlib::get_nth_element<N>` and then the list of args `args...`. Bear in mind that this function happens at compile time as it has the `constexpr` keyword applied to it. This is a meta-programming library after all!

The function `mlib::get_nth_element` is relatively easy to implement, you can find it on [godbolt](https://www.godbolt.org/z/zadoz8fcz) which also has a bit more detail on the matter, here is the implementation of `mlib::get_nth_element`:
```C++
namespace mlib {
template <auto N> constexpr auto get_nth_element(auto... args) {
    return [&]<std::size_t... Indexes>(std::index_sequence<Indexes...>) {
        return [](decltype((void *)Indexes)... DontCare, auto *arg,
                  auto *...DontCareEither) { return *arg; }(&args...);
    }(std::make_index_sequence<N>{});
}
} // namespace mlib
```
---------------------------------------------------------------------------------------------------
