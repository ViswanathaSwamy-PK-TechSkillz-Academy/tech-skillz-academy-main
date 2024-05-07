# Data Structures and Algorithms

Certainly! Let's dive into the concepts of **Big O**, **Big Omega**, and **Big Theta** notations, along with some JavaScript examples.

1. **Big O (O)**:
   - **Definition**: Big O notation represents the **upper bound** on an algorithm's time complexity relative to its input size. It describes how the execution time grows as the input size increases.
   - **Mathematical Representation**: If a function `f(n)` describes the running time of an algorithm, we say that `f(n)` is `O(g(n))` if there exist positive constants `C` and `n₀` such that `0 ≤ f(n) ≤ Cg(n)` for all `n ≥ n₀`.
   - **Example**: Consider linear search in an array. The worst-case time complexity is `O(n)` because the search time grows linearly with the input size.
   - **JavaScript Example**:

     ```javascript
     function linearSearch(arr, target) {
         for (let i = 0; i < arr.length; i++) {
             if (arr[i] === target) {
                 return i; // Found at index i
             }
         }
         return -1; // Not found
     }
     ```

2. **Big Omega (Ω)**:
   - **Definition**: Big Omega notation represents the **lower bound** on an algorithm's time complexity. It indicates the least amount of time required (best-case scenario).
   - **Mathematical Representation**: If `f(n)` is the running time of an algorithm, we say that `f(n)` is `Ω(g(n))` if there exist positive constants `C` and `n₀` such that `0 ≤ Cg(n) ≤ f(n)` for all `n ≥ n₀`.
   - **Example**: Binary search has a best-case time complexity of `Ω(log n)` because it efficiently narrows down the search space by half with each iteration.
   - **JavaScript Example**:

     ```javascript
     function binarySearch(arr, target) {
         let left = 0;
         let right = arr.length - 1;
         while (left <= right) {
             const mid = Math.floor((left + right) / 2);
             if (arr[mid] === target) {
                 return mid; // Found at index mid
             } else if (arr[mid] < target) {
                 left = mid + 1;
             } else {
                 right = mid - 1;
             }
         }
         return -1; // Not found
     }
     ```

3. **Big Theta (Θ)**:
   - **Definition**: Big Theta notation represents the **tightest bound** on an algorithm's time complexity. It combines both the upper and lower bounds.
   - **Mathematical Representation**: If `f(n)` is both `O(g(n))` and `Ω(g(n))`, we say that `f(n)` is `Θ(g(n))`.
   - **Example**: Merge sort has a time complexity of `Θ(n log n)` because it consistently performs well in both best and worst cases.
   - **JavaScript Example**:

     ```javascript
     function mergeSort(arr) {
         if (arr.length <= 1) {
             return arr;
         }
         const mid = Math.floor(arr.length / 2);
         const left = mergeSort(arr.slice(0, mid));
         const right = mergeSort(arr.slice(mid));
         return merge(left, right);
     }

     function merge(left, right) {
         const result = [];
         let i = 0;
         let j = 0;
         while (i < left.length && j < right.length) {
             if (left[i] < right[j]) {
                 result.push(left[i]);
                 i++;
             } else {
                 result.push(right[j]);
                 j++;
             }
         }
         return result.concat(left.slice(i), right.slice(j));
     }
     ```

Remember that these notations help us analyze and compare algorithms based on their efficiency. Feel free to use these examples in your notes! 🚀¹⁸⁹ [^10^] ¹¹¹²

Source: Conversation with Bing, 5/7/2024
(1) Difference between Big O vs Big Theta Θ vs Big Omega Ω Notations. <https://www.geeksforgeeks.org/difference-between-big-oh-big-omega-and-big-theta/>.
(2) JavaScript - Big-O Cheat Sheet - 30 seconds of code. <https://www.30secondsofcode.org/js/s/big-o-cheatsheet/>.
(3) What does Big O - O(log N) complexity mean? - GeeksforGeeks. <https://www.geeksforgeeks.org/what-does-big-o-olog-n-complexity-mean/>.
(4) Big O Cheat Sheet – Time Complexity Chart - freeCodeCamp.org. <https://www.freecodecamp.org/news/big-o-cheat-sheet-time-complexity-chart/>.
(5) algorithm - Big O notation log(n^2) = O(log(n)) - Stack Overflow. <https://stackoverflow.com/questions/49604973/big-o-notation-logn2-ologn>.
(6) performance - O(log N) == O(1) - Why not? - Stack Overflow. <https://stackoverflow.com/questions/1491795/olog-n-o1-why-not>.
(7) Big-O, Little-o, Theta, Omega · Data Structures and Algorithms. <https://bing.com/search?q=Omega+Theta+O>.
(8) Big-O, Little-o, Theta, Omega · Data Structures and Algorithms. <https://cathyatseneca.gitbooks.io/data-structures-and-algorithms/content/analysis/notations.html>.
(9) 9.3 Big-O, Omega, and Theta - University of Toronto. <https://www.teach.cs.toronto.edu/~csc110y/fall/notes/09-runtime/03-asymptotic-notation.html>.
(10) What’s the Difference Between Big O, Big Omega, and Big Theta?. <https://jarednielsen.com/big-o-omega-theta/>.
(11) Asymptotic Notations - Big Oh, Omega, and Theta - CodeCrucks. <https://codecrucks.com/asymptotic-notations/>.
(12) Asymptotic Notations - Theta, Big O and Omega | Studytonight. <https://www.studytonight.com/data-structures/aysmptotic-notations>.
(13) github.com. <https://github.com/pinglu85/algorithms-and-data-structures/tree/30301520b3ca436ff5491a746d1cacf08dda519d/algorithms%2Fsorting_algorithms%2FmergeSort.js>.
(14) github.com. <https://github.com/PhishyFish/algorithms-and-data-structures/tree/1a57d5907736cf7cbe34a6cbab6495a7e2b65623/merge_sort.js>.
(15) github.com. <https://github.com/shamim-ahmed/algocasts-udemy/tree/c135a757d92077d3af3a2126b2b1c90e9b31eb21/exercises%2Fsorting%2Findex.js>.
(16) github.com. <https://github.com/ShivarajaHM/javascript/tree/790ecd67e1d9fcf2e774e8f9a6fd92e17b03cf91/Utility%2Futility.js>.
(17) github.com. <https://github.com/ChronisYan/Algorithms-Data-Structures/tree/b27685e0a612b3ff68a58407633e3f6a29d9e108/javascript%2Falgorithms%2Fsorting.js>.
