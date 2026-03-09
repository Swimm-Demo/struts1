---
title: Wildcard pattern matching flow
---
This document describes the flow for matching an input string against a wildcard pattern and extracting matched segments. Pattern matching enables flexible routing and filtering by allowing strings to be compared against patterns with wildcards. When a match is successful, relevant segments are extracted and returned in a map.

# Pattern Matching Setup and Initial Checks

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start: Attempt to match input to
wildcard pattern"]
    click node1 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:159:205"
    
    subgraph loop1["For each segment in the pattern"]
        node1 --> node2{"Does input match this pattern segment?"}
        
        node2 -->|"Yes"| node3["Extract and store matched segment"]
        click node3 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:279:298"
        node2 -->|"No"| node5["Return: No match"]
        click node5 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:215:277"
        node3 --> node4{"Is this the last pattern segment?"}
        
        node4 -->|"Yes"| node6["Return: Match success with extracted
segments"]
        click node6 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:240:256"
        node4 -->|"No"| node2
    end

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
click node2 goToHeading "Forward Subarray Search Logic"
node2:::HeadingStyle
click node4 goToHeading "Backward Subarray Search Logic"
node4:::HeadingStyle

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start: Attempt to match input to
%% wildcard pattern"]
%%     click node1 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:159:205"
%%     
%%     subgraph loop1["For each segment in the pattern"]
%%         node1 --> node2{"Does input match this pattern segment?"}
%%         
%%         node2 -->|"Yes"| node3["Extract and store matched segment"]
%%         click node3 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:279:298"
%%         node2 -->|"No"| node5["Return: No match"]
%%         click node5 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:215:277"
%%         node3 --> node4{"Is this the last pattern segment?"}
%%         
%%         node4 -->|"Yes"| node6["Return: Match success with extracted
%% segments"]
%%         click node6 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:240:256"
%%         node4 -->|"No"| node2
%%     end
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
%% click node2 goToHeading "Forward Subarray Search Logic"
%% node2:::HeadingStyle
%% click node4 goToHeading "Backward Subarray Search Logic"
%% node4:::HeadingStyle
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/WildcardHelper.java" line="159">

---

In <SwmToken path="core/src/main/java/org/apache/struts/util/WildcardHelper.java" pos="159:5:5" line-data="    public boolean match(Map map, String data, int[] expr) {">`match`</SwmToken>, the code checks for nulls, sets up buffers, and parses the expr array to find where the first special MATCH\_\* constant appears. It also handles <SwmToken path="core/src/main/java/org/apache/struts/util/WildcardHelper.java" pos="193:9:9" line-data="        // First check for MATCH_BEGIN">`MATCH_BEGIN`</SwmToken> if present, which means the pattern must match from the start of the input. The expr array is a mix of literal char codes and special tokens, so this section is about prepping for the main matching loop and figuring out where to start.

```java
    public boolean match(Map map, String data, int[] expr) {
        if (map == null) {
            throw new NullPointerException("No map provided");
        }

        if (data == null) {
            throw new NullPointerException("No data provided");
        }

        if (expr == null) {
            throw new NullPointerException("No pattern expression provided");
        }

        char[] buff = data.toCharArray();

        // Allocate the result buffer
        char[] rslt = new char[expr.length + buff.length];

        // The previous and current position of the expression character
        // (MATCH_*)
        int charpos = 0;

        // The position in the expression, input, translation and result arrays
        int exprpos = 0;
        int buffpos = 0;
        int rsltpos = 0;
        int offset = -1;

        // The matching count
        int mcount = 0;

        // We want the complete data be in {0}
        map.put(Integer.toString(mcount), data);

        // First check for MATCH_BEGIN
        boolean matchBegin = false;

        if (expr[charpos] == MATCH_BEGIN) {
            matchBegin = true;
            exprpos = ++charpos;
        }

        // Search the fist expression character (except MATCH_BEGIN - already
        // skipped)
        while (expr[charpos] >= 0) {
            charpos++;
        }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/WildcardHelper.java" line="207">

---

After setup, the code checks if the current expr segment matches the input (using <SwmToken path="core/src/main/java/org/apache/struts/util/WildcardHelper.java" pos="214:5:5" line-data="                if (!matchArray(expr, exprpos, charpos, buff, buffpos)) {">`matchArray`</SwmToken> for anchored patterns or <SwmToken path="core/src/main/java/org/apache/struts/util/WildcardHelper.java" pos="220:5:5" line-data="                offset = indexOfArray(expr, exprpos, charpos, buff, buffpos);">`indexOfArray`</SwmToken> for flexible ones). It then handles <SwmToken path="core/src/main/java/org/apache/struts/util/WildcardHelper.java" pos="240:8:8" line-data="            if (exprchr == MATCH_END) {">`MATCH_END`</SwmToken> and <SwmToken path="core/src/main/java/org/apache/struts/util/WildcardHelper.java" pos="248:12:12" line-data="            } else if (exprchr == MATCH_THEEND) {">`MATCH_THEEND`</SwmToken> to decide if the match is done, or continues to the next segment. This is where the actual matching logic starts looping through the expr array, chunk by chunk.

```java
        // The expression charater (MATCH_*)
        int exprchr = expr[charpos];

        while (true) {
            // Check if the data in the expression array before the current
            // expression character matches the data in the input buffer
            if (matchBegin) {
                if (!matchArray(expr, exprpos, charpos, buff, buffpos)) {
                    return (false);
                }

                matchBegin = false;
            } else {
                offset = indexOfArray(expr, exprpos, charpos, buff, buffpos);

                if (offset < 0) {
                    return (false);
                }
            }

            // Check for MATCH_BEGIN
            if (matchBegin) {
                if (offset != 0) {
                    return (false);
                }

                matchBegin = false;
            }

            // Advance buffpos
            buffpos += (charpos - exprpos);

            // Check for END's
            if (exprchr == MATCH_END) {
                if (rsltpos > 0) {
                    map.put(Integer.toString(++mcount),
                        new String(rslt, 0, rsltpos));
                }

                // Don't care about rest of input buffer
                return (true);
            } else if (exprchr == MATCH_THEEND) {
                if (rsltpos > 0) {
                    map.put(Integer.toString(++mcount),
                        new String(rslt, 0, rsltpos));
                }

                // Check that we reach buffer's end
                return (buffpos == buff.length);
            }

            // Search the next expression character
            exprpos = ++charpos;

            while (expr[charpos] >= 0) {
                charpos++;
            }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/WildcardHelper.java" line="265">

---

Here the code decides if it should search forward (<SwmToken path="core/src/main/java/org/apache/struts/util/WildcardHelper.java" pos="272:3:3" line-data="                ? indexOfArray(expr, exprpos, charpos, buff, buffpos)">`indexOfArray`</SwmToken>) or backward (<SwmToken path="core/src/main/java/org/apache/struts/util/WildcardHelper.java" pos="273:3:3" line-data="                : lastIndexOfArray(expr, exprpos, charpos, buff, buffpos);">`lastIndexOfArray`</SwmToken>) for the next pattern segment, based on the previous MATCH\_\* token. This is how it finds where the next literal or wildcard chunk appears in the input.

```java
            int prevchr = exprchr;

            exprchr = expr[charpos];

            // We have here prevchr == * or **.
            offset =
                (prevchr == MATCH_FILE)
                ? indexOfArray(expr, exprpos, charpos, buff, buffpos)
```

---

</SwmSnippet>

## Forward Subarray Search Logic

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start: Validate search range"]
    click node1 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:318:321"
    node1 --> node2{"Is search range valid?"}
    click node2 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:319:321"
    node2 -->|"No"| node3["Stop: Invalid range"]
    click node3 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:320:321"
    node2 -->|"Yes"| node4{"Is sequence empty?"}
    click node4 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:324:325"
    node4 -->|"Yes"| node5["Return: End of array"]
    click node5 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:325:326"
    node4 -->|"No"| node6{"Is sequence length 1?"}
    click node6 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:329:335"
    node6 -->|"Yes"| node7["Search for single character in array"]
    click node7 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:331:334"
    node7 --> node8["Return: Position found or not found"]
    click node8 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:333:335"
    node6 -->|"No"| loop1
    
    subgraph loop1["For each possible position in array"]
      node9{"Does sequence match at current position?"}
      click node9 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:340:354"
      node9 -->|"Yes"| node10["Return: Position found"]
      click node10 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:348:349"
      node9 -->|"No"| node11["Move to next position"]
      click node11 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:357:358"
      node11 --> node9
    end
    loop1 --> node12["Return: Not found if end reached"]
    click node12 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:358:358"

classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start: Validate search range"]
%%     click node1 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:318:321"
%%     node1 --> node2{"Is search range valid?"}
%%     click node2 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:319:321"
%%     node2 -->|"No"| node3["Stop: Invalid range"]
%%     click node3 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:320:321"
%%     node2 -->|"Yes"| node4{"Is sequence empty?"}
%%     click node4 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:324:325"
%%     node4 -->|"Yes"| node5["Return: End of array"]
%%     click node5 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:325:326"
%%     node4 -->|"No"| node6{"Is sequence length 1?"}
%%     click node6 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:329:335"
%%     node6 -->|"Yes"| node7["Search for single character in array"]
%%     click node7 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:331:334"
%%     node7 --> node8["Return: Position found or not found"]
%%     click node8 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:333:335"
%%     node6 -->|"No"| loop1
%%     
%%     subgraph loop1["For each possible position in array"]
%%       node9{"Does sequence match at current position?"}
%%       click node9 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:340:354"
%%       node9 -->|"Yes"| node10["Return: Position found"]
%%       click node10 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:348:349"
%%       node9 -->|"No"| node11["Move to next position"]
%%       click node11 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:357:358"
%%       node11 --> node9
%%     end
%%     loop1 --> node12["Return: Not found if end reached"]
%%     click node12 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:358:358"
%% 
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/WildcardHelper.java" line="316">

---

In <SwmToken path="core/src/main/java/org/apache/struts/util/WildcardHelper.java" pos="316:5:5" line-data="    protected int indexOfArray(int[] r, int rpos, int rend, char[] d,">`indexOfArray`</SwmToken>, the code handles edge cases: if the subarray is empty, it returns <SwmToken path="core/src/main/java/org/apache/struts/util/WildcardHelper.java" pos="325:4:6" line-data="            return (d.length); //?? dpos?">`d.length`</SwmToken>; if it's a single char, it searches for that char. Otherwise, it's prepping for the main substring search. The int array is compared to the char array directly, which only works because of how expr is built.

```java
    protected int indexOfArray(int[] r, int rpos, int rend, char[] d,
        int dpos) {
        // Check if pos and len are legal
        if (rend < rpos) {
            throw new IllegalArgumentException("rend < rpos");
        }

        // If we need to match a zero length string return current dpos
        if (rend == rpos) {
            return (d.length); //?? dpos?
        }

        // If we need to match a 1 char length string do it simply
        if ((rend - rpos) == 1) {
            // Search for the specified character
            for (int x = dpos; x < d.length; x++) {
                if (r[rpos] == d[x]) {
                    return (x);
                }
            }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/WildcardHelper.java" line="338">

---

The main loop here walks through the input, looking for the first spot where the expr subarray matches the input. If it finds a match, it returns the start index; if not, it returns -1.

```java
        // Main string matching loop. It gets executed if the characters to
        // match are less then the characters left in the d buffer
        while (((dpos + rend) - rpos) <= d.length) {
            // Set current startpoint in d
            int y = dpos;

            // Check every character in d for equity. If the string is matched
            // return dpos
            for (int x = rpos; x <= rend; x++) {
                if (x == rend) {
                    return (dpos);
                }

                if (r[x] != d[y++]) {
                    break;
                }
            }

            // Increase dpos to search for the same string at next offset
            dpos++;
        }
```

---

</SwmSnippet>

## Handling Forward and Backward Search Results

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/WildcardHelper.java" line="273">

---

Back in WildcardHelper.match, after getting a result from <SwmToken path="core/src/main/java/org/apache/struts/util/WildcardHelper.java" pos="220:5:5" line-data="                offset = indexOfArray(expr, exprpos, charpos, buff, buffpos);">`indexOfArray`</SwmToken>, the code may call <SwmToken path="core/src/main/java/org/apache/struts/util/WildcardHelper.java" pos="273:3:3" line-data="                : lastIndexOfArray(expr, exprpos, charpos, buff, buffpos);">`lastIndexOfArray`</SwmToken> for greedy matching (like with double-star wildcards). If offset is negative, it means no match was found, so the function returns false.

```java
                : lastIndexOfArray(expr, exprpos, charpos, buff, buffpos);

            if (offset < 0) {
                return (false);
            }

```

---

</SwmSnippet>

## Backward Subarray Search Logic

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    node1["Start: Find last occurrence of sequence
in array"]
    click node1 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:380:381"
    node1 --> node2{"Is search range valid?"}
    click node2 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:383:385"
    node2 -->|"No"| node3["Exit: Invalid search range"]
    click node3 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:384:385"
    node2 -->|"Yes"| node4{"Is sequence empty?"}
    click node4 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:388:389"
    node4 -->|"Yes"| node5["Return end of array (no characters to
match)"]
    click node5 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:389:390"
    node4 -->|"No"| node6{"Is sequence length 1?"}
    click node6 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:393:399"
    node6 -->|"Yes"| node7["Search for last occurrence of character"]
    click node7 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:395:398"
    node7 -->|"Found"| node10["Return position"]
    click node10 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:397:398"
    node7 -->|"Not found"| node11["Return not found"]
    click node11 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:399:399"
    node6 -->|"No"| node8["Search for last occurrence of sequence"]
    click node8 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:404:424"
    subgraph loop1["For each possible position in array,
from end to start"]
      node8 --> node9{"Does sequence match at this position?"}
      click node9 openCode "core/src/main/java/org/apache/struts/util/WildcardHelper.java:412:419"
      node9 -->|"Yes"| node10
      node9 -->|"No"| node8
    end
    node8 -->|"Not found after loop"| node11
classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;

%% Swimm:
%% %%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
%% flowchart TD
%%     node1["Start: Find last occurrence of sequence
%% in array"]
%%     click node1 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:380:381"
%%     node1 --> node2{"Is search range valid?"}
%%     click node2 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:383:385"
%%     node2 -->|"No"| node3["Exit: Invalid search range"]
%%     click node3 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:384:385"
%%     node2 -->|"Yes"| node4{"Is sequence empty?"}
%%     click node4 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:388:389"
%%     node4 -->|"Yes"| node5["Return end of array (no characters to
%% match)"]
%%     click node5 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:389:390"
%%     node4 -->|"No"| node6{"Is sequence length 1?"}
%%     click node6 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:393:399"
%%     node6 -->|"Yes"| node7["Search for last occurrence of character"]
%%     click node7 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:395:398"
%%     node7 -->|"Found"| node10["Return position"]
%%     click node10 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:397:398"
%%     node7 -->|"Not found"| node11["Return not found"]
%%     click node11 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:399:399"
%%     node6 -->|"No"| node8["Search for last occurrence of sequence"]
%%     click node8 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:404:424"
%%     subgraph loop1["For each possible position in array,
%% from end to start"]
%%       node8 --> node9{"Does sequence match at this position?"}
%%       click node9 openCode "<SwmPath>[core/…/util/WildcardHelper.java](core/src/main/java/org/apache/struts/util/WildcardHelper.java)</SwmPath>:412:419"
%%       node9 -->|"Yes"| node10
%%       node9 -->|"No"| node8
%%     end
%%     node8 -->|"Not found after loop"| node11
%% classDef HeadingStyle fill:#777777,stroke:#333,stroke-width:2px;
```

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/WildcardHelper.java" line="380">

---

In <SwmToken path="core/src/main/java/org/apache/struts/util/WildcardHelper.java" pos="380:5:5" line-data="    protected int lastIndexOfArray(int[] r, int rpos, int rend, char[] d,">`lastIndexOfArray`</SwmToken>, the code checks for edge cases (empty or single-char subarrays), then searches backward through the input for the last occurrence of the expr subarray. The int/char comparison is a quirk of how patterns are encoded here.

```java
    protected int lastIndexOfArray(int[] r, int rpos, int rend, char[] d,
        int dpos) {
        // Check if pos and len are legal
        if (rend < rpos) {
            throw new IllegalArgumentException("rend < rpos");
        }

        // If we need to match a zero length string return current dpos
        if (rend == rpos) {
            return (d.length); //?? dpos?
        }

        // If we need to match a 1 char length string do it simply
        if ((rend - rpos) == 1) {
            // Search for the specified character
            for (int x = d.length - 1; x > dpos; x--) {
                if (r[rpos] == d[x]) {
                    return (x);
                }
            }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/WildcardHelper.java" line="402">

---

This loop checks each possible position from the end back to dpos, looking for a match. If it finds one, it returns the start index; if not, it returns -1.

```java
        // Main string matching loop. It gets executed if the characters to
        // match are less then the characters left in the d buffer
        int l = d.length - (rend - rpos);

        while (l >= dpos) {
            // Set current startpoint in d
            int y = l;

            // Check every character in d for equity. If the string is matched
            // return dpos
            for (int x = rpos; x <= rend; x++) {
                if (x == rend) {
                    return (l);
                }

                if (r[x] != d[y++]) {
                    break;
                }
            }

            // Decrease l to search for the same string at next offset
            l--;
        }
```

---

</SwmSnippet>

## Copying Matched Segments and Advancing

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/WildcardHelper.java" line="279">

---

Just returned from <SwmToken path="core/src/main/java/org/apache/struts/util/WildcardHelper.java" pos="273:3:3" line-data="                : lastIndexOfArray(expr, exprpos, charpos, buff, buffpos);">`lastIndexOfArray`</SwmToken>, WildcardHelper.match now copies the matched segment into the result buffer. For <SwmToken path="core/src/main/java/org/apache/struts/util/WildcardHelper.java" pos="281:8:8" line-data="            if (prevchr == MATCH_PATH) {">`MATCH_PATH`</SwmToken>, it copies everything up to the match boundary.

```java
            // Copy the data from the source buffer into the result buffer
            // to substitute the expression character
            if (prevchr == MATCH_PATH) {
                while (buffpos < offset) {
                    rslt[rsltpos++] = buff[buffpos++];
                }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/WildcardHelper.java" line="286">

---

For <SwmToken path="core/src/main/java/org/apache/struts/util/WildcardHelper.java" pos="271:6:6" line-data="                (prevchr == MATCH_FILE)">`MATCH_FILE`</SwmToken>, the code checks for '/' in the matched segment—if it finds one, it bails out (returns false), since file wildcards shouldn't cross path boundaries. Otherwise, it copies the segment to the result buffer.

```java
                // Matching file, don't copy '/'
                while (buffpos < offset) {
                    if (buff[buffpos] == '/') {
                        return (false);
                    }

                    rslt[rsltpos++] = buff[buffpos++];
                }
```

---

</SwmSnippet>

<SwmSnippet path="/core/src/main/java/org/apache/struts/util/WildcardHelper.java" line="296">

---

Finally, if everything matches, the function puts the last matched segment in the map and returns true. If any step fails, it returns false. The MATCH\_\* constants control how the matching and segment extraction work throughout.

```java
            map.put(Integer.toString(++mcount), new String(rslt, 0, rsltpos));
            rsltpos = 0;
        }
    }
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc3RydXRzMSUzQSUzQVN3aW1tLURlbW8=" repo-name="struts1"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
