Download Link: https://assignmentchef.com/product/solved-csci376-assignment-2-basic-kernel-programming
<br>
<h1>Task 1 – Basic Kernel Programming</h1>

Task 1a

Write a program using OpenCL that uses a kernel to fill in the contents of an array of 512 numbers in parallel. The program is to prompt the user to enter a number between 1 and 100 (inclusive). The program is to check whether the user entered a valid number, if not, the program should quit. If a valid number was entered, enqueue a kernel (using the enqueueNDRangeKernel function) that accepts the number and an array, and fills in the contents of the array using the number (and the work-items’ global ID) as follows:

<ul>

 <li>If the user enters 1, the resulting contents of the array should be: 3, 4, 5, 6, 7,… until 514</li>

 <li>If the user enters 2, the resulting contents of the array should be: 3, 5, 7, 9, 11,… until 1025</li>

 <li>If the user enters 3, the resulting contents of the array should be: 3, 6, 9, 12, 15,… until 1536</li>

 <li>…</li>

 <li>If the user enters 100, the resulting contents of the array should be: 3, 103, 203, 303, 403,… until 51103</li>

</ul>

After kernel execution, display the contents of the output array on screen.




Task 1b

Write a program using OpenCL to do the following:

<ul>

 <li>Host code.</li>

</ul>

o Create two STL vectors, named vec1 and vec2. Initialise their contents as follows:

<ul>

 <li>vec1: A vector of ints that contains 32 elements. Initialise the elements with random values between 10 and 20.</li>

 <li>vec2: A vector of ints that contains 16 elements. Initialise the first half of the vector with values from 2 to 9 and the second half with values from -9 to -2.</li>

</ul>

<ul>

 <li>Create and initialise the necessary memory objects to pass vec1 and vec2 as input to the kernel, and another memory object for kernel output.</li>

 <li>Enqueue the kernel such that each work-item will process 8-elements from vec1, respectively, i.e. work-item 1 will refer to elements 0-7, work-item 2 will refer to elements 8-15, etc.</li>

 <li>After kernel execution, obtain the output from the kernel and display the results on screen.</li>

</ul>




 Kernel code.

<ul>

 <li>The kernel is to have three parameters.

  <ul>

   <li>input1: an input array of type int4  input2: an input array of type int</li>

   <li>output: an output array of type int o When the kernel is enqueued by the host, the contents of vec1 and vec2 are to be passed to the kernel as input1 and input2, respectively.</li>

  </ul></li>

 <li>In the kernel, copy the contents from input1 and input2 into three private OpenCL vectors of type int8:</li>

</ul>

 v: is to contain 8 elements obtained from input1. The content of v will vary based on the work-item, such that

<ul>

 <li>for work-time 1, v will contain vectors 0-1 from input1</li>

</ul>

(Note: the contents of two int4 vectors, gives a total of 8 elements)  for work-time 2, v will contain vectors 2-3 from input1  etc.

<ul>

 <li>v1: is to contain elements 0-7 from input2 (use the vloadn function)</li>

 <li>v2: is to contain elements 8-15 from input2 (use the vloadn function) o Create an int8 vector, named results. The contents of this vector should be filled as follows:</li>

 <li>Check whether any of the elements in v are greater than 17</li>

</ul>

<ul>

 <li>If there are, then for elements in v that are greater than 17, copy the corresponding elements from v1; for elements less than or equal to 17, copy the corresponding elements from v2. (use the select function)</li>

 <li>Otherwise, fill the first 4 elements with the contents from the first 4 elements of v1 and the next 4 elements with contents from the first 4</li>

</ul>

elements of v2.

o Stores the contents of v, v1, v2 and results in the output array (use the vstoren function). Note that each work-item will have to store its respective results in the output array.




<h1>Example output</h1>

Work-item 0

v  : 18 19 19 11 17 11 20 10 v1  :  2  3  4  5  6  7  8  9  v2  : -9 -8 -7 -6 -5 -4 -3 -2 results :  2  3  4 -6 -5 -4  8 -2




Work-item 1

v  : 11 10 15 16 17 13 14 12 v1  :  2  3  4  5  6  7  8  9  v2  : -9 -8 -7 -6 -5 -4 -3 -2 results :  2  3  4  5 -9 -8 -7 -6




Work-item 2

…







<h1>Task 2 – Shift Cipher</h1>

A shift cipher (a.k.a. Caesar’s cipher) is a simple substitution cipher in which each letter in the plaintext is replaced with another letter that is located a certain number, n, positions away in the alphabet. The value of n can be positive or negative. For positive values, replace letters with letters located n places on its right (i.e. ‘shifted’ by n positions to the right). For negative values, replace letters with letters located n places on its left. If it reaches the end/start of the alphabets, wrap around to the start/end.

For example: If n = -3, each letter in the plaintext is replaced with a letter 3 positions before that letter in the alphabet list.

Plaintext:           The ‘quick’ brown fox jumps over the “lazy” dog.

Ciphertext:  QEB ‘NRFZH’ YOLTK CLU GRJMP LSBO QEB  “IXWV” ALD. Decrypted:  THE ‘QUICK’ BROWN FOX JUMPS OVER THE “LAZY” DOG.

Note that in the example above, c → Z, since 3 positions before ‘c’ wraps around to the end of the alphabet list and continues from ‘Z’. Similarly, a → X and b → Y.

Leave anything that is not an alphabet as is (i.e. punctuations and spaces should remain in the encrypted/decrypted text without alteration).<a href="#_ftn1" name="_ftnref1"><sup>[1]</sup></a> The encrypted/decrypted text should all be in upper case.

Decrypting the ciphertext is simply a matter of reversing the shift.







Task 2a

Write a normal C/C++ program (not using OpenCL) that reads the contents from a text file called “plaintext.txt” (a test file has been provided). The program should prompt the user to input a valid n value, then encrypt the plaintext using the shift cipher method described above, and output the ciphertext into an output text file called “ciphertext.txt”. To ensure that the encryption was performed correctly, your program must also decrypt the ciphertext into a file called “decrypted.txt” to check whether it matches the original plaintext (albeit in upper case).

Task 2b

Write an OpenCL program to perform the same functionality as in Task 2a, but in parallel. Note that it is more efficient to use OpenCL vector data types for processing in the kernel, as less workitems will be required and elements in a vector can be processed simultaneously.

Task 2c

Write an OpenCL program to perform parallel encryption, and decryption, by substituting characters based on the following lookup table:

<table width="627">

 <tbody>

  <tr>

   <td width="25">a</td>

   <td width="23">b</td>

   <td width="23">c</td>

   <td width="25">d</td>

   <td width="23">e</td>

   <td width="24">f</td>

   <td width="23">g</td>

   <td width="25">h</td>

   <td width="29">i</td>

   <td width="23">j</td>

   <td width="23">k</td>

   <td width="28">l</td>

   <td width="27">m</td>

   <td width="24">n</td>

   <td width="23">o</td>

   <td width="24">p</td>

   <td width="23">q</td>

   <td width="25">r</td>

   <td width="23">s</td>

   <td width="25">t</td>

   <td width="23">u</td>

   <td width="23">v</td>

   <td width="26">w</td>

   <td width="24">x</td>

   <td width="23">y</td>

   <td width="23">z</td>

  </tr>

  <tr>

   <td width="25">G</td>

   <td width="23">X</td>

   <td width="23">S</td>

   <td width="25">Q</td>

   <td width="23">F</td>

   <td width="24">A</td>

   <td width="23">R</td>

   <td width="25">O</td>

   <td width="29">W</td>

   <td width="23">B</td>

   <td width="23">L</td>

   <td width="28">M</td>

   <td width="27">T</td>

   <td width="24">H</td>

   <td width="23">C</td>

   <td width="24">V</td>

   <td width="23">P</td>

   <td width="25">N</td>

   <td width="23">Z</td>

   <td width="25">U</td>

   <td width="23">I</td>

   <td width="23">E</td>

   <td width="26">Y</td>

   <td width="24">D</td>

   <td width="23">K</td>

   <td width="23">J</td>

  </tr>

 </tbody>

</table>

Based on the table above, for encryption the letter a (or A) will be replaced by G, b (or B) will be replaced by X, c (or C) will be replaced by S, etc. As with Task 2b, it is more efficient to use OpenCL vector data types for processing in the kernel.

)





