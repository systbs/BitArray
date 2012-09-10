**C code for bit arrays**
=========================

https://github.com/noporpoise/BitArray/

License: revised BSD

Isaac Turner <turner.isaac@gmail.com>

About
=====

Bit arrays are arrays of bits (values zero or one).  This is a convenient and
efficient implementation for C/C++.  Arrays can be enlarged or shrunk as needed.

Bit arrays are initialised to zero when created or extended.  All operations
have their bounds checked - an "Out of bounds" error is printed if you try to
access a bit with index >= length.

Please get in touch if you have suggestions / requests / bugs.  

Build
=====

To build and run the test code:

    make
    ./bit_array_test

Using bit_array in your code
============================

Add to the top of your code:

    #include "bit_array.h"

Add to your compiler arguments

    BIT_ARR_PATH=path/to/bit_array/
    gcc ... -I$(BIT_ARR_PATH) -L$(BIT_ARR_PATH) -libbitarr

You can then call the following methods:

Constructor - create a new bit array of length nbits

    BIT_ARRAY* bit_array_create(bit_index_t nbits);

Destructor - free the memory used for a bit array

    void bit_array_free(BIT_ARRAY* bitarray);

Get length of bit array

    bit_index_t bit_array_length(const BIT_ARRAY* bit_arr);

Enlarge or shrink the size of a bit array. 
Shrinking will free some memory if it is large.
Enlarging an array will add zeros to the end of it.
Returns 1 on success, 0 on failure

    char bit_array_resize(BIT_ARRAY* bitarr, bit_index_t new_num_of_bits);

Set/Get bits
------------

Get the value of a bit (returns 0 or 1)

    char bit_array_get_bit(const BIT_ARRAY* bitarr, bit_index_t b);

Set a bit (to 1) at position b

    void bit_array_set_bit(BIT_ARRAY* bitarr, bit_index_t b);

Clear a bit (to 0) at position b

    void bit_array_clear_bit(BIT_ARRAY* bitarr, bit_index_t b);

Set multiple bits at once. 

    void bit_array_set_bits(BIT_ARRAY* bitarr, size_t n, ...);
    
    // e.g.
    bit_array_set_bits(bitarr, 3, {1,20,31});

Clear multiple bits at once.

    void bit_array_clear_bits(BIT_ARRAY* bitarr, size_t n, ...);

    // e.g.
    bit_array_clear_bits(bitarr, 3, {1,20,31});

Set all bits in this array to 0

    void bit_array_clear_all(BIT_ARRAY* bitarr);

Set all bits in this array to 1

    void bit_array_set_all(BIT_ARRAY* bitarr);

Clear all the bits in a region

    void bit_array_clear_region(BIT_ARRAY* bitarr,
                                bit_index_t start, bit_index_t length);

Set all the bits in a region

    void bit_array_set_region(BIT_ARRAY* bitarr,
                              bit_index_t start, bit_index_t length);

Get a word of a given size.  First bit is in the least significant bit position
start index must be within the range of the bit array (0 <= x < length)

    uint64_t bit_array_word64(const BIT_ARRAY* bitarr, bit_index_t start);
    uint32_t bit_array_word32(const BIT_ARRAY* bitarr, bit_index_t start);
    uint16_t bit_array_word16(const BIT_ARRAY* bitarr, bit_index_t start);
    uint8_t  bit_array_word8 (const BIT_ARRAY* bitarr, bit_index_t start);

Set 64 bits at once from a particular start position

    void bit_array_set_word64(BIT_ARRAY* bitarr, bit_index_t start, uint64_t word);

Count bits set
--------------

Get the number of bits set (hamming weight)

    bit_index_t bit_array_num_bits_set(const BIT_ARRAY* bitarr);

Get the number of bits not set (length - hamming weight)

    bit_index_t bit_array_num_bits_cleared(const BIT_ARRAY* bitarr);

Find the index of the first bit that is set.  
Returns 1 if a bit is set, otherwise 0. 
Index of first set bit is stored in the integer pointed to by `result`.
If no bit is set `result` is not changed and zero is returned.

    char bit_array_find_first_set_bit(const BIT_ARRAY* bitarr, bit_index_t* result);

Sorting
-------

Put all the 0s before all the 1s

    void bit_array_sort_bits(BIT_ARRAY* bitarr);

Put all the 1s before all the 0s

    void bit_array_rev_sort_bits(BIT_ARRAY* bitarr);


String functions
----------------

Get this array as a string (remember to free the result!)

    char* bit_array_to_string(const BIT_ARRAY* bitarr);

Copy to a string.  Warning: does not null-terminate string.

    void bit_array_cpy_to_string(const BIT_ARRAY* bitarr, char* str,
                                 bit_index_t start, bit_index_t length);

Print this array to a file stream.  Prints '0's and '1'.  Doesn't print newline.

    void bit_array_print(const BIT_ARRAY* bitarr, FILE* fout);

Create a bit array from a string of '0's and '1's e.g. "01001010110".  
Remember to free the result!

    BIT_ARRAY* bit_array_from_string(const char* bitstr);

Clone/copy
----------

Copy a BIT_ARRAY struct and the data it holds - returns pointer to new object

    BIT_ARRAY* bit_array_clone(const BIT_ARRAY* bitarr);

Copy bits from one array to another.
Destination and source can be the same bit_array and src/dst regions can overlap

    void bit_array_copy(BIT_ARRAY* dst, bit_index_t dstindx,
                        const BIT_ARRAY* src, bit_index_t srcindx,
                        bit_index_t length);

Logic operators
---------------

Destination and source bit arrays must be of the same length, however they may
point to the same object

    void bit_array_and(BIT_ARRAY* dest, const BIT_ARRAY* src1, const BIT_ARRAY* src2);
    void bit_array_or(BIT_ARRAY* dest, const BIT_ARRAY* src1, const BIT_ARRAY* src2);
    void bit_array_xor(BIT_ARRAY* dest, const BIT_ARRAY* src1, const BIT_ARRAY* src2);
    void bit_array_not(BIT_ARRAY* dest, const BIT_ARRAY* src);

Compare two bit arrays by value stored.
Arrays do not have to be the same length (e.g. 101 (5) > 00000011 (3))

    int bit_array_cmp(const BIT_ARRAY* bitarr1, const BIT_ARRAY* bitarr2);


Shift array left/right with a given `fill` (0 or 1)

    void bit_array_shift_right(BIT_ARRAY* bitarr, bit_index_t shift_dist, char fill);
    void bit_array_shift_left(BIT_ARRAY* bitarr, bit_index_t shift_dist, char fill);


Read/Write bit_array to a file
------------------------------

File format is [8 bytes: for number of elements in array][data].
Number of bytes of data is: (int)((num_of_bits + 7) / 8) -- i.e. rounddown(num_of_bits)

Saves bit array to a file.  Returns the number of bytes written

    bit_index_t bit_array_save(const BIT_ARRAY* bitarr, FILE* f);

Reads bit array from a file.  Returns bit array or NULL on failure

    BIT_ARRAY* bit_array_load(FILE* f);

Coming soon
-----------

Under development

    void bit_array_cycle_right(BIT_ARRAY* bitarr, const bit_index_t dist);
    void bit_array_cycle_left(BIT_ARRAY* bitarr, const bit_index_t dist);

Revised BSD License
===================

 Copyright (c) 2011, Isaac Turner
 All rights reserved.

 Redistribution and use in source and binary forms, with or without
 modification, are permitted provided that the following conditions are met:
    * Redistributions of source code must retain the above copyright
      notice, this list of conditions and the following disclaimer.

 THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND
 ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
 WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
 DISCLAIMED. IN NO EVENT SHALL <COPYRIGHT HOLDER> BE LIABLE FOR ANY
 DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES
 (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES;
 LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND
 ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
 (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS
 SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

Development
===========

To do:
* cycle left / right
* reverse array, reverse region

Need re-writing:
* bit_array_add
* bit_array_increment
* bit_array_decrement

Also:
* write test cases for each method (test cases to go in bit_array_test.c)