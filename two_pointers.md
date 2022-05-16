For most substring problem, we are given a string and need to find a substring of it which satisfy some restrictions. A general way is to use a hashmap assisted with two pointers. The template is given below.

<!-- write code block -->
```cpp
int findSubstring(string s){
    vector<int> map(128,0);
    int counter; // check whether the substring is valid
    int begin=0, end=0; //two pointers, one point to tail and one  head
    int d; //the length of substring

    for() { /* initialize the hash map here */ }

    while(end<s.size()){

        if(map[s[end++]]-- ?){  /* modify counter here */ }

        while(/* counter condition */){ 
                
                /* update d here if finding minimum*/

            //increase begin to make it invalid/valid again
            
            if(map[s[begin++]]++ ?){ /*modify counter here*/ }
        }  

        /* update d here if finding maximum*/
    }
    return d;
}
```

```cpp
string minWindow(string s, string t) {
        vector<int> map(128,0);
        for(auto c: t) map[c]++;
        int counter=t.size(), begin=0, end=0, d=INT_MAX, head=0;
        while(end<s.size()){
            if(map[s[end++]]-->0) counter--; //in t
            while(counter==0){ //valid
                if(end-begin<d)  d=end-(head=begin);
                if(map[s[begin++]]++==0) counter++;  //make it invalid
            }  
        }
        return d==INT_MAX? "":s.substr(head, d);
    }
```

One thing needs to be mentioned is that when asked to find maximum substring, we should update maximum after the inner while loop to guarantee that the substring is valid. On the other hand, when asked to find minimum substring, we should update minimum inside the inner while loop.

The code of solving Longest Substring with At Most Two Distinct Characters is below:

```cpp
int lengthOfLongestSubstringTwoDistinct(string s) {
        vector<int> map(128, 0);
        int counter=0, begin=0, end=0, d=0; 
        while(end<s.size()){
            if(map[s[end++]]++==0) counter++;
            while(counter>2) if(map[s[begin++]]--==1) counter--;
            d=max(d, end-begin);
        }
        return d;
    }
```
The code of solving Longest Substring Without Repeating Characters is below:

Update 01.04.2016, thanks @weiyi3 for advise.
```cpp
int lengthOfLongestSubstring(string s) {
        vector<int> map(128,0);
        int counter=0, begin=0, end=0, d=0; 
        while(end<s.size()){
            if(map[s[end++]]++>0) counter++; 
            while(counter>0) if(map[s[begin++]]-->1) counter--;
            d=max(d, end-begin); //while valid, update d
        }
        return d;
    }
```


**Alternate explanation**
```cpp
public static String minWindowHash(String s, String t) {
    // start, end ... two pointers that form the sliding window (which we want to check if it contains all
    // characters in t and if it is the smallest observed one)
    int start = 0, end = 0;

    // counter monitors the total number of characters in t that are missing in current sliding window
    int counter = t.length();

    // two variables to store information about smallest valid window observed until now
    int minStart = 0, minLength = Integer.MAX_VALUE;

    // we want to know both which characters are missing, and how many of them are missing in the current
    // window. To monitor this we use a hashmap. Keys are unique characters in t, and values are their occurrences
    Map<Character, Integer> map = new HashMap<>();
    for (char c : t.toCharArray()) {
        map.put(c, map.getOrDefault(c, 0)+1);
    }

    // all interesting windows are discovered when "end" is increased to s.length()
    while (end < s.length()) {
        char currentEndingChar = s.charAt(end);

        // if the character pointed to by "end" is contained in t
        if (map.containsKey(currentEndingChar)) {
            // and we are still missing that character (hence its value in map is larger than 0), we decrease counter
            if (map.get(currentEndingChar)>0) counter--;

            // We decrease its value by one.
            // value = 0 means that the number of this character inside current window is equal to the total number of it
            // contained in t
            // value = -2 means that we have two extra duplicates of that character in the window. So when we start to
            // shrink the window, leaving this character two times won't invalidate the resulting window!
            map.put(currentEndingChar, map.get(currentEndingChar)-1);
        }

        // If all characters from t are found in current window ("start" to "end"), we want to start reduce the window's
        // size by increasing "start"
        while(counter == 0) {
            // of course, check if the current valid window is the smallest
            if (end - start < minLength) {
                minStart = start;

                // Make sure that the minLength is the actual length of the window
                minLength = end - start + 1;
            }

            // Now, since we plan to make the window smaller by increasing "start", we want to know two things:
            // 1. Is the character, which we are leaving now, contained in t?
            // 2. If it is contained in t, how many of its duplicates are contained in the current window? If there
            // are plenty of them, leaving it (start++) won't immediately invalidate the resulting window.
            if (map.containsKey(s.charAt(start))) {

                // If the character pointed to by "start" is of interest, since we are leaving it, we increase its
                // value in the map by 1, to indicate that we miss it once
                map.put(s.charAt(start), map.get(s.charAt(start))+1);

                // If the value is greater than 0, that means the next window is no more valid, and we need to move
                // "end" forward to find the next valid window (in the next iteration)
                if (map.get(s.charAt(start))>0) counter++;
            }

            // After all checks, we reduce window size by increasing "start" by 1
            start++;
        }

        // The while loop is left, so the current window is not valid and we need to increment "end" to look for
        // the next valid window.
        end++;
    }

    if (minLength != Integer.MAX_VALUE) return s.substring(minStart, minStart + minLength);
    return "";
}
```

Here is the java version based on templated code with optimisation (using array instead of map). The runtime dropped for e.g. in min window from 143ms -> 7ms.

**Minimum window**
```java
  public String minWindow(String s, String t) {
    int [] map = new int[128];
    for (char c : t.toCharArray()) {
      map[c]++;
    }
    int start = 0, end = 0, minStart = 0, minLen = Integer.MAX_VALUE, counter = t.length();
    while (end < s.length()) {
      final char c1 = s.charAt(end);
      if (map[c1] > 0) counter--;
      map[c1]--;
      end++;
      while (counter == 0) {
        if (minLen > end - start) {
          minLen = end - start;
          minStart = start;
        }
        final char c2 = s.charAt(start);
        map[c2]++;
        if (map[c2] > 0) counter++;
        start++;
      }
    }

    return minLen == Integer.MAX_VALUE ? "" : s.substring(minStart, minStart + minLen);
  }
```
**Longest Substring - at most K distinct characters**

```java
  public int lengthOfLongestSubstringKDistinct(String s, int k) {
    int[] map = new int[256];
    int start = 0, end = 0, maxLen = Integer.MIN_VALUE, counter = 0;

    while (end < s.length()) {
      final char c1 = s.charAt(end);
      if (map[c1] == 0) counter++;
      map[c1]++;
      end++;

      while (counter > k) {
        final char c2 = s.charAt(start);
        if (map[c2] == 1) counter--;
        map[c2]--;
        start++;
      }

      maxLen = Math.max(maxLen, end - start);
    }

    return maxLen;
  }
```
**Longest Substring - at most 2 distinct characters**

```java
public int lengthOfLongestSubstringTwoDistinct(String s) {
    int[] map = new int[128];
    int start = 0, end = 0, maxLen = 0, counter = 0;

    while (end < s.length()) {
      final char c1 = s.charAt(end);
      if (map[c1] == 0) counter++;
      map[c1]++;
      end++;

      while (counter > 2) {
        final char c2 = s.charAt(start);
        if (map[c2] == 1) counter--;
        map[c2]--;
        start++;
      }

      maxLen = Math.max(maxLen, end - start);
    }

    return maxLen;
  }
```

**Longest Substring - without repeating characters**
```java
  public int lengthOfLongestSubstring2(String s) {
    int[] map = new int[128];
    int start = 0, end = 0, maxLen = 0, counter = 0;

    while (end < s.length()) {
      final char c1 = s.charAt(end);
      if (map[c1] > 0) counter++;
      map[c1]++;
      end++;

      while (counter > 0) {
        final char c2 = s.charAt(start);
        if (map[c2] > 1) counter--;
        map[c2]--;
        start++;
      }

      maxLen = Math.max(maxLen, end - start);
    }

    return maxLen;
  }
```