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
