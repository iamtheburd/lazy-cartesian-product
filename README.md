# lazy-cartesian-product
A small .hpp library that can generate combinations with a focus on memory-efficiency

## Introduction
When generating data, it may be necessary to create a set of distinct possible combinations. This can be useful for statistics, filling up a database, or running some function over the a combination (like generating cost data given combination `{x, y, z}`). A lot of libraries exist to do this, but memory space can quickly run out when the amount of combinations rises to a very large number.

The library also focuses on using STL utilities from the C++ language without the need for extra overhead (like Boost).

If you would like to see how this library was implemented, [combigen](https://github.com/iamtheburd/combigen) is a CLI tool that helps generate distinct combinations and output them to `.csv` or `.json` format.

## Usage
Input will always be a `vector<vector<string>>`. This keeps things nice and simple, and you can always convert back as necessary in your own projects. There are some public functions available from the library:

* `entry_at` - Generates the *nth* combination at entry `index`
* `generate_samples` - Generates a random (distinct, evenly spread out) subset of possible combinations of size `sample_size`
* `compute_max_size` - Computes the maximum amount of possible combinations
* `generate_random_indices` - Given the desired sample size and the maximum size, this function will return a `vector<long>` containaining an evenly-distributed list of indices throughout the range given.

This project is also licensed under the MIT license, so feel free to use and change this however you please.

## Installation
Simply place the `.hpp` file somewhere in your project and include the class:

```
...
#include "lazy-cartesian-product.hpp"
...


using lazycp::lazy-cartesian-product; // For easier access
...
```

When compiling your project, ensure that it compiles to the C++11 standard (`-std=c++11` flag for GCC). 

## Example Usage
While `lazy-cartesian-product` can be used in many situations, below are a few examples showcasing some of the functions of the library:

### Basic Example
First, we can generate a possible combination at the *nth* entry. For this example, we can find the combination of pizza components at the 5th entry.
```
#include <string>
#include <vector>
#include <iostream>
#include <lazy-cartesian-product.hpp>   // Assumes the .hpp file is in the same directory

using std::cout;
using std::vector;

using lazycp::lazy_cartesian_product;   // For easier readability


int main (int argc, char* argv[])
{
     vector<string> crusts   = { "Thin", "Thick", "Cheese-stuffed", "Garlic-rubbed" };
     vector<string> sauces   = { "Marinara", "BBQ", "None" };
     vector<string> cheeses  = { "Mozarella", "Cheddar", "Parmesan" };
     vector<string> toppings = { "Extra Cheese", "Chicken", "Pepperoni", "Sausage" };

     // Put all of the vectors together so lazy-cartesian-product can accept the input
     vector<vector<string>> possibilities = { crusts, sauces, cheeses, toppings };

     // Show the combination at the 5th index
     vector<string> result = lazy_cartesian_product::entry_at(possibilities, 5);

     // Display this result:
     //    "Mozarella, Thin, BBQ, Chicken, "
     for (const string &entry: result)
     {
        cout << result << ", ";
     }
     cout << "\n";
 
     return 0;
}
```

### Generate Random Sample (Performance)
Next, we will generate a random sample of combinations. This is especially useful
for data analysis when data needs to be generated in an evenly-distributed manner.
This example focuses on high-performance at the expense of memory usage (for large-computations, of course).

```
#include <string>
#include <vector>
#include <iostream>
#include <lazy-cartesian-product.hpp>   

using std::cout;
using std::vector;

using lazycp::lazy_cartesian_product;   


int main (int argc, char* argv[])
{
     
          
     vector<string> crusts   = { "Thin", "Thick", "Cheese-stuffed", "Garlic-rubbed" };
     vector<string> sauces   = { "Marinara", "BBQ", "None" };
     vector<string> cheeses  = { "Mozarella", "Cheddar", "Parmesan" };
     vector<string> toppings = { "Extra Cheese", "Chicken", "Pepperoni", "Sausage" };

     vector<vector<string>> possibilities = { crusts, sauces, cheeses, toppings };
    
     // Generate 10 random samples
     vector<vector<string>> random_samples = lazy_cartesian_product::generate_samples(possibilities, 10);

     /* Displayed these results:
            Mozarella, Thin, Marinara, Sausage,
            Mozarella, Cheese-stuffed, Marinara, Chicken,
            Mozarella, Cheese-stuffed, None, Extra Cheese,
            Cheddar, Thin, None, Chicken
            Cheddar, Thick, Marinara, Extra Cheese
            Cheddar, Cheese-stuffed, None, Sausage
            Cheddar, Garlic-rubbed, BBQ, Sausage
            Cheddar, Garlic-rubbed, None, Extra Cheese
            Parmesan, Thin, BBQ, Extra Cheese
            Parmesan, Cheese-stuffed, BBQ, Chicken
      */
     for (const vector<string> &entry: random_samples) 
     {
        for (const string &item: entry)
        {
            cout << item << ", ";
        }
        cout << "\n";
     }
     return 0;
}
```

### Generate Random Sample (Memory-optimized)
Finally, we can generate a random sample of combinations but do so without
consuming a lot of memory. By using some of the provided functions, we can
sacrifice a small amount of computing time so that very little memory space
is used. As such, the only limiters that remain are I/O performance and availability
of disk space. This is especially useful for lower-end machines.

```
#include <string>
#include <vector>
#include <iostream>
#include <lazy-cartesian-product.hpp>   

using std::cout;
using std::vector;

using lazycp::lazy_cartesian_product;   


int main (int argc, char* argv[])
{
     
           
     vector<string> crusts   = { "Thin", "Thick", "Cheese-stuffed", "Garlic-rubbed" };
     vector<string> sauces   = { "Marinara", "BBQ", "None" };
     vector<string> cheeses  = { "Mozarella", "Cheddar", "Parmesan" };
     vector<string> toppings = { "Extra Cheese", "Chicken", "Pepperoni", "Sausage" };

     vector<vector<string>> possibilities = { crusts, sauces, cheeses, toppings };

     // First, find the max size of the combinations
     long max_size = lazy_cartesian_product::compute_max_size(possiblities);

     // Next, generate a list of random indices to find
     vector<long> random_indices = lazy_cartesian_product::generate_random_indices(100, max_size);
    
     // Finally, iterate through the random indices, generate a combination, and display it
     for (const long &index: random_indices)
     {
        vector<string> entry = lazy_cartesian_product::entry_at(possibilities, index);
        for (const string &item: entry)
        {
            cout << item << ", ";
        }
        cout << "\n";
     }
     return 0;
}
```

## TODOs
* Add better exception-handling
* Add testing framework

## Contributing
Pull-requests are always welcome!

## License
MIT License

Copyright (c) 2018 Tyler Burdsall

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
