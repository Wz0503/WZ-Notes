# leetcode 动态规划

# 357、计算各各位数不同的数字个数

> ```
> import java.ut(il.*;
> class Solution {
>  public int countNumbersWithUniqueDigits(int n) {
>      if (n == 0)
>          return 1;
>      if (n == 1)
>          return 10;
>      int[] dp = new int[n+1];
>      dp[0] = 1;
>      dp[1] = 9;
>      int sum = dp[0]+dp[1];
>      for (int i = 2; i <= n; i++){
>          dp[i] = dp[i-1] * (11 - i);
>          sum += dp[i];
>      }
>      return sum;
>  }
> }
>
>
> 注：dp[0] = 1;
> dp[1] = 9;
> dp[2] = 9*9;
> dp[3] = 9*9*8;
> ...
> ```

# 42、连续子数组的最大和

```
class Solution {
    public int maxSubArray(int[] nums) {
        if (nums == null || nums.length == 0)
            return 0;
        int[] dp = new int[nums.length];
        dp[0] = nums[0];
        int max = dp[0];
        for (int i = 1; i < nums.length; i++){
            if (dp[i-1] <= 0){
                dp[i] = nums[i];
            } else {
                dp[i] = dp[i-1] + nums[i];
            }
             max = Math.max(dp[i], max);
        }
        return max;
    }
}

注：dp[i] = dp[i-1] + nums[i]  ,dp[i-1]>0
    dp[i] = nums[i],            dp[i-1] <= 0
```



# 5、最长回文子串

给定一个字符串 `s`，找到 `s` 中最长的回文子串。你可以假设 `s` 的最大长度为 1000。

```
public String longestPalindrome(String s) {
    if (s == null || s.length() < 1) return "";
    int start = 0, end = 0;
    for (int i = 0; i < s.length(); i++) {
        int len1 = expandAroundCenter(s, i, i);
        int len2 = expandAroundCenter(s, i, i + 1);
        int len = Math.max(len1, len2);
        if (len > end - start) {
            start = i - (len - 1) / 2;
            end = i + len / 2;
        }
    }
    return s.substring(start, end + 1);
}

private int expandAroundCenter(String s, int left, int right) {
    int L = left, R = right;
    while (L >= 0 && R < s.length() && s.charAt(L) == s.charAt(R)) {
        L--;
        R++;
    }
    return R - L - 1;
}

注：中心扩展法
```

# 6、Z字形变换

 将一个给定字符串根据给定的行数，以从上往下、从左到右进行 Z 字形排列。

比如输入字符串为 `"LEETCODEISHIRING"` 行数为 3 时，排列如下：

```
L   C   I   R
E T O E S I I G
E   D   H   N

```

之后，你的输出需要从左往右逐行读取，产生出一个新的字符串，比如：`"LCIRETOESIIGEDHN"`。

```
 public String convert(String s, int numRows) {
        if (numRows < 2)
            return s;
        List<StringBuilder> list = new ArrayList<StringBuilder>();
        for (int i = 0; i < numRows; i++){
            list.add(new StringBuilder());
        }
        int i = 0;
        int flag = -1;
        for (char c: s.toCharArray()){
            list.get(i).append(c);
            if (i == 0 || i == numRows-1){
                flag = -flag;
            }
            i += flag;
        }
        StringBuilder sb = new StringBuilder();
        for (StringBuilder s1: list){
            sb.append(s1);
        }
        return sb.toString();

    }
```



# 8、字符串转换整数

首先，该函数会根据需要丢弃无用的开头空格字符，直到寻找到第一个非空格的字符为止。

当我们寻找到的第一个非空字符为正或者负号时，则将该符号与之后面尽可能多的连续数字组合起来，作为该整数的正负号；假如第一个非空字符是数字，则直接将其与之后连续的数字字符组合起来，形成整数。

```
public int myAtoi(String str) {
		if(str==null) {
			return 0;
		}
		int n = str.length();
		int i = 0;
		int res = 0;
		boolean is_negative = false;
		//第一步，跳过前面若干个空格
		while(i<n && str.charAt(i)==' ') {
			++i;
		}
		//如果字符串全是空格直接返回
		if(i==n) {
			return 0;
		}
		//第二步，判断正负号
		if(str.charAt(i)=='-') {
			is_negative = true;
		}
		//如果是正负号，还需要将指针i，跳过一位
		if(str.charAt(i)=='-' || str.charAt(i)=='+') {
			++i;
		}
		//第三步，循环判断字符是否在 0~9之间
		while(i<n && str.charAt(i)>='0' && str.charAt(i)<='9') {
			//'0'的ASCII码是48，'1'的是49，这么一减就从就可以得到真正的整数值
			int tmp = str.charAt(i)-48;
			//判断是否大于 最大32位整数
			if(!is_negative &&(res>214748364 ||(res==214748364 && tmp>=7))) {
				return 2147483647;
			}
			//判断是否小于 最小32位整数
			if(is_negative &&(-res<-214748364 || (-res==-214748364 && tmp>=8))) {
				return -2147483648;
			}
			res = res*10 + tmp;
			++i;
		}
		//如果有负号标记则返回负数
		if(is_negative) {
			return -res;
		}
		return res;
	}
```



# 38、外观数列

「外观数列」是一个整数序列，从数字 1 开始，序列中的每一项都是对前一项的描述。前五项如下：

```
1.     1
2.     11
3.     21
4.     1211
5.     111221

```

`1` 被读作  `"one 1"`  (`"一个一"`) , 即 `11`。
`11` 被读作 `"two 1s"` (`"两个一"`）, 即 `21`。
`21` 被读作 `"one 2"`,  "`one 1"` （`"一个二"` ,  `"一个一"`) , 即 `1211`。

```
class Solution {
    public String countAndSay(int n) {
        if(n==1){
           return "1";
        }
        return say(countAndSay(n-1));
    }

    private String say(String s){
        //记录数值出现的次数
        int count=1;
        //当前的数值
        char num = s.charAt(0);
        StringBuilder sb = new StringBuilder();
        for(int i =1;i<s.length();i++){
            char c = s.charAt(i);
            if(c==num){
                count++;               
            }else{
                sb.append(count);   
                sb.append(num);         
                num = c;
                count=1;
            }
        }
        sb.append(count);   
        sb.append(num);
        return sb.toString();
    }
}
```



# 43、字符串相乘

```
public static String multiply(String num1, String num2) {
        if (num1.equals("0") || num2.equals("0")) {
            return "0";
        }
        // 保存计算结果
        String res = "0";

        // num2 逐位与 num1 相乘
        for (int i = num2.length() - 1; i >= 0; i--) {
            int carry = 0;
            // 保存 num2 第i位数字与 num1 相乘的结果
            StringBuilder temp = new StringBuilder();
            // 补 0
            for (int j = 0; j < num2.length() - 1 - i; j++) {
                temp.append(0);
            }
            int n2 = num2.charAt(i) - '0';

            // num2 的第 i 位数字 n2 与 num1 相乘
            for (int j = num1.length() - 1; j >= 0 || carry != 0; j--) {
                int n1 = j < 0 ? 0 : num1.charAt(j) - '0';
                int product = (n1 * n2 + carry) % 10;
                temp.append(product);
                carry = (n1 * n2 + carry) / 10;
            }
            // 将当前结果与新计算的结果求和作为新的结果
            res = addStrings(res, temp.reverse().toString());
        }
        return res;
    }

    /**
     * 对两个字符串数字进行相加，返回字符串形式的和
     */
    public static String addStrings(String num1, String num2) {
        StringBuilder builder = new StringBuilder();
        int carry = 0;
        for (int i = num1.length() - 1, j = num2.length() - 1;
             i >= 0 || j >= 0 || carry != 0;
             i--, j--) {
            int x = i < 0 ? 0 : num1.charAt(i) - '0';
            int y = j < 0 ? 0 : num2.charAt(j) - '0';
            int sum = (x + y + carry) % 10;
            builder.append(sum);
            carry = (x + y + carry) / 10;
        }
        return builder.reverse().toString();
    }
```



# 49、字母异位词分组

给定一个字符串数组，将字母异位词组合在一起。字母异位词指字母相同，但排列不同的字符串。

**示例:**

```
输入: ["eat", "tea", "tan", "ate", "nat", "bat"],
输出:
[
  ["ate","eat","tea"],
  ["nat","tan"],
  ["bat"]
]
```

```
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        if (strs.length == 0) return new ArrayList();
        Map<String, List> ans = new HashMap<String, List>();
        for (String s : strs) {
            char[] ca = s.toCharArray();
            Arrays.sort(ca);
            String key = String.valueOf(ca);
            if (!ans.containsKey(key)) ans.put(key, new ArrayList());
            ans.get(key).add(s);
        }
        return new ArrayList(ans.values());
    }
}
```



# 125、验证回文串

给定一个字符串，验证它是否是回文串，只考虑字母和数字字符，可以忽略字母的大小写。

```
class Solution {
    public boolean isPalindrome(String s) {
        int l = 0;
        int r = s.length() - 1;
        while(l < r){
            //过滤掉不是字母或数字的元素
            while(l < r && !Character.isLetterOrDigit(s.charAt(l))){
                l++;
            }
            while(l < r && !Character.isLetterOrDigit(s.charAt(r))){
                r--;
            }
            //都转化成小写进行比较
            if(Character.toLowerCase(s.charAt(l)) != Character.toLowerCase(s.charAt(r))){
                return false;
            }
            l++;
            r--;
        }
        return true;
    }
}
```



# 151、翻转字符串

给定一个字符串，逐个翻转字符串中的每个单词。

```
class Solution {
    public String reverseWords(String s) {
        if (s == null || s.length() == 0)
            return "";
        String[] strs = s.split("\\s+");
        if (strs.length == 0)
            return "";
        String res = strs[strs.length-1];
        for (int i = strs.length - 2; i >= 0; i--){
            res = res + " " + strs[i];
        }
        return res.trim();
    }
}
```



# 165、比较版本号

比较两个版本号 *version1 *和 *version2*。
如果 `*version1 *> *version2*` 返回 `1`，如果 `*version1 *< *version2*` 返回 `-1`， 除此之外返回 `0`。

你可以假设版本字符串非空，并且只包含数字和 `.` 字符。

 `.` 字符不代表小数点，而是用于分隔数字序列。

例如，`2.5` 不是“两个半”，也不是“差一半到三”，而是第二版中的第五个小版本。

```
public int compareVersion(String version1, String version2) {
    String[] nums1 = version1.split("\\.");
    String[] nums2 = version2.split("\\.");
    int i = 0, j = 0;
    while (i < nums1.length || j < nums2.length) {
        //这个技巧经常用到，当一个已经遍历结束的话，我们将其赋值为 0
        String num1 = i < nums1.length ? nums1[i] : "0";
        String num2 = j < nums2.length ? nums2[j] : "0";
        int res = compare(num1, num2);
        if (res == 0) {
            i++;
            j++;
        } else {
            return res;
        }
    }
    return 0;
}

private int compare(String num1, String num2) {
    int n1 = Integer.parseInt(num1);
    int n2 = Integer.parseInt(num2);
    if (n1 > n2) {
        return 1;
    } else if (n1 < n2) {
        return -1;
    } else {
        return 0;
    }
}

```

# 55、跳跃游戏

给定一个非负整数数组，你最初位于数组的第一个位置。

数组中的每个元素代表你在该位置可以跳跃的最大长度。

判断你是否能够到达最后一个位置。

```
class Solution {
    public boolean canJump(int[] nums) {
        if (nums == null || nums.length == 0)
            return false;
        int len = nums.length;
        boolean[] dp = new boolean[len];
        dp[0] = true;
        for (int i = 0; i < nums.length; i++){
            if (dp[i]){
                for (int j = i; j <= i + nums[i] && j <len; j++){
                    dp[j] = true;
                }
            }
            if (dp[len-1])
                return true;
        }
        return dp[len-1];
    }
}
```



# 147、对链表进行插入排序

```
class Solution {
    public ListNode insertionSortList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }

        // 插入排序是迭代的，每次只移动一个元素，直到所有元素可以形成一个有序的输出列表。
        // 每次迭代中，插入排序只从输入数据中移除一个待排序的元素，找到它在序列中适当的位置，并将其插入。
        // 每次迭代完成，从插入元素为止，该链表可以被认为已经部分排序
        // 重复直到所有输入数据插入完为止

         // 1.遍历并与前面已经有序的序列向前逐一比较排序，找到合适为止插入

        // 定义三个指针 pre, cur, lat
        //pre    cur    lat
        // h  ->  4  ->  2  ->  5  ->  3  ->  null

        // 创建 h 节点，用于遍历链表
        ListNode h = new ListNode(-1);
        h.next = head;
        ListNode pre = h;
        ListNode cur = head;
        ListNode lat;

        while (cur != null) {
            lat = cur.next; // 记录下一个要插入排序的值

            if (lat != null && lat.val < cur.val) { // 只有 cur.next 比 cur 小才需要向前寻找插入点
                // 寻找插入点，从 pre 开始遍历 （每次都是头节点 h 开始向后遍历，因为单向链表是无法从后往前遍）
                while (pre.next != null && pre.next.val < lat.val) { // 如果当前节点的值小于要插入排序的值
                    pre = pre.next; // 继续向后移动
                }

                // 找到要插入的位置，此时 pre 节点后面的位置就是 lat 要插入的位置
                // 交换 pre 跟 lat 节点需要一个 temp 节点来临时保存下插入位置 node 后 next
                ListNode tmp = pre.next;
                // 在 pre 节点后面插入
                pre.next = lat;
                // 此时 cur 节点还是 pre 所在的节点，所以其 next 要指向插入节点 lat 指向的 next
                cur.next = lat.next;
                // 插入let节点后，把记录的原先插入位置后续的 next 节点传给它
                lat.next = tmp;
                // 由于每次都是从前往后找插入位置，但是单向链表是无法从后往前遍历，所以需要每次插入完成后要让 pre 复位
                pre = h;
            } else {
                // 都这直接把 cur 指针指向到下一个
                cur = lat;
            }
        }

       return h.next;
    }
}
```



# 349、两个数组的交集

给定两个数组，编写一个函数来计算它们的交集

```
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        Set<Integer> set = new HashSet<Integer>();
        if (nums1.length == 0 || nums2.length == 0)
            return new int[]{};
        Set<Integer> s1 = new HashSet<Integer>();
        for (int i = 0; i < nums1.length; i++){
            s1.add(nums1[i]);
        }
        int j = 0;
        while (j < nums2.length){
            if (s1.contains(nums2[j])){
                set.add(nums2[j]);
                j++;
            } else{
                j++;
            }
        }
        Object[] obj = set.toArray();
        int[] res = new int[obj.length];
        for (int i = 0; i < obj.length; i++){
            res[i] = (int)obj[i];
        }
        return res;
    }
}
```



# 973、最接近原点的点

我们有一个由平面上的点组成的列表 points。需要从中找出 K 个距离原点 (0, 0) 最近的点。

（这里，平面上两点之间的距离是欧几里德距离。）

你可以按任何顺序返回答案。除了点坐标的顺序之外，答案确保是唯一的。

```
class Solution {
    public int[][] kClosest(int[][] points, int K) {
        int n = points.length;
        int[] dists = new int[n];
        for (int i = 0; i < n; i++){
            dists[i] = points[i][0] * points[i][0] + points[i][1] * points[i][1];
        }
        Arrays.sort(dists);
        int min = dists[K-1];
        int[][] res = new int[K][2];
        int t = 0;
        for (int i = 0; i < n; i++){
            if (points[i][0] * points[i][0] + points[i][1] * points[i][1] <= min){
                res[t] = points[i];
                t++;
            }
        }
        return res;

    }
}
```



# 204、计算质数

统计所有小于非负整数 *n *的质数的数量。

```
class Solution {
    public int countPrimes(int n) {
        boolean[] res = new boolean[n];
        Arrays.fill(res, true);

        for (int i = 2; i < n; i++){
            if (res[i]){
                for (int j = 2 * i; j < n; j+=i){
                    res[j] = false;
                }
            }
        }
        int count = 0;
        for (int i = 2; i < n; i++){
            if (res[i])
                count++;
        }
        return count;

    }

}
```



# 95、不同的二叉搜索树II

```
public List<TreeNode> generateTrees(int n) {
    List<TreeNode> ans = new ArrayList<TreeNode>();
    if (n == 0) {
        return ans;
    }
    return getAns(1, n);

}

private List<TreeNode> getAns(int start, int end) {
    List<TreeNode> ans = new ArrayList<TreeNode>();
    //此时没有数字，将 null 加入结果中
    if (start > end) {
        ans.add(null);
        return ans;
    }
    //只有一个数字，当前数字作为一棵树加入结果中
    if (start == end) {
        TreeNode tree = new TreeNode(start);
        ans.add(tree);
        return ans;
    }
    //尝试每个数字作为根节点
    for (int i = start; i <= end; i++) {
        //得到所有可能的左子树
        List<TreeNode> leftTrees = getAns(start, i - 1);
         //得到所有可能的右子树
        List<TreeNode> rightTrees = getAns(i + 1, end);
        //左子树右子树两两组合
        for (TreeNode leftTree : leftTrees) {
            for (TreeNode rightTree : rightTrees) {
                TreeNode root = new TreeNode(i);
                root.left = leftTree;
                root.right = rightTree;
                //加入到最终结果中
                ans.add(root);
            }
        }
    }
    return ans;
}

```



# 96、不同的二叉搜索树

```
public class Solution {
  public int numTrees(int n) {
    int[] G = new int[n + 1];
    G[0] = 1;
    G[1] = 1;

    for (int i = 2; i <= n; ++i) {
      for (int j = 1; j <= i; ++j) {
        G[i] += G[j - 1] * G[i - j];
      }
    }
    return G[n];
  }
}
```

# 328、 奇偶链表

```
public ListNode oddEvenList(ListNode head) {
        ListNode lhead = new ListNode(0);
        ListNode rhead = new ListNode(0);
        ListNode lnode = lhead;
        ListNode rnode = rhead;
        ListNode node = head;
        int count = 1;
        while (node != null){
            if (count % 2 == 1){
                lnode.next = node;
                lnode = node;
            } else {
                rnode.next = node;
                rnode = node;
            }
            count++;
            node = node.next;
        }
        rnode.next = null;
        lnode.next = rhead.next;
        return lhead.next;
    }
```

# 445、两数相加II

```
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        Stack<Integer> stack1 = new Stack<>();
        Stack<Integer> stack2 = new Stack<>();
        while (l1 != null){
            stack1.push(l1.val);
            l1 = l1.next;
        }
        while (l2 != null){
            stack2.push(l2.val);
            l2 = l2.next;
        }

        boolean carry = false;
        int v1,v2;
        ListNode pre = null;
        while (!stack1.isEmpty() || !stack2.isEmpty()){
            if (stack1.isEmpty()){
                v1 = 0;
            } else {
                v1 = stack1.pop();
            }
            if (stack2.isEmpty()){
                v2 = 0;
            } else{
                v2 = stack2.pop();
            }
            int d = v1+v2;
            if (carry){
                d++;
            }
            if (d >= 10){
                carry = true;
            } else {
                carry = false;
            }
            ListNode newNode = new ListNode(d%10);
            newNode.next = pre;
            pre = newNode;
        }
        if (carry){
            ListNode node = new ListNode(1);
            node.next = pre;
            pre = node;
        }
        return pre;

    }
```

# 83、删除排序链表中的重复元素

```
public ListNode deleteDuplicates(ListNode head) {
        if (head == null || head.next == null){
            return head;
        }

        ListNode pre = head;
        ListNode curr = head.next;
        ListNode next = null;
        while (curr != null){
            if (curr.val != pre.val){
                pre = curr;
                curr = curr.next;
            } else {
                pre.next = curr.next;
                curr = curr.next;
            }
        }
        return head;
    }
```

# 19、删除链表的倒数第N个节点

```
 public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode slow = head;
        ListNode fast = head;

        int i = 1;
        while (i <= n){
            fast = fast.next;
            i++;
            if (fast == null){
                return head.next;
            }
        }
        while (fast.next != null){
            fast = fast.next;
            slow = slow.next;
        }
        slow.next = slow.next.next;
        return head;

    }
```

# 203、移除链表元素

```
 public ListNode removeElements(ListNode head, int val) {
        ListNode first = new ListNode(0);
        first.next = head;
        ListNode pre = first;

        while (head != null){
            if (head.val == val){
                pre.next = head.next;
            } else {
                pre = head;
            }
            head = head.next;
        }
        return first.next;
    }
```

# 350、两个数组的交集II

```
public int[] intersect(int[] nums1, int[] nums2) {
        Arrays.sort(nums1);
        Arrays.sort(nums2);

        int len1 = nums1.length;
        int len2 = nums2.length;

        int i = 0;
        int j = 0;
        List<Integer> list = new ArrayList<>();
        while (i < len1 && j < len2){
            if (nums1[i] == nums2[j]){
                list.add(nums1[i]);
                i++;
                j++;
            } else if (nums1[i] < nums2[j]){
                i++;
            } else {
                j++;
            }
        }
        int n = list.size();
        int[] result = new int[n];
        for (int k = 0; k < n; k++){
            result[k] = list.get(k);
        }
        return result;
    }
```

# 24、两两交换链表中的节点

```
public ListNode swapPairs(ListNode head) {
        ListNode single = new ListNode(0);
        single.next = head;

        ListNode pre = single;

        ListNode first;
        ListNode second;

        while ((head != null) && (head.next != null)){
            first = head;
            second = head.next;

            pre.next = second;
            first.next = second.next;
            second.next = first;

            pre = first;
            head = first.next;

        }
        return single.next;
    }
```

# 143、重排数组

```
public void reorderList(ListNode head) {
        Map<Integer, ListNode> map = new HashMap<>();

        ListNode single = new ListNode(0);
        single.next = head;
        ListNode cur = single;

        int i = 0;
        while (head != null){
            map.put(i, head);
            i++;
            head = head.next;
        }

        for (int j = 0; j < i/2; j++){
            ListNode first = map.get(j);
            ListNode second = map.get(i-j-1);
            cur.next = first;
            cur.next.next = second;
            cur = second;
        }
        if (i % 2 == 1){
            ListNode temp = map.get(i/2);
            cur.next = temp;
            cur = temp;
        }
        cur.next = null;
        head = single.next;
    }
```

# 297、二叉树的序列化和反序列化

```
// Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        if (root == null){
            return "#,";
        }
        String res = root.val + ",";
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        while (!queue.isEmpty()){
            root = queue.poll();
            if (root.left != null){
                res += root.left.val + ",";
                queue.offer(root.left);
            } else {
                res += "#,";
            }
            if (root.right != null){
                res += root.right.val + ",";
                queue.offer(root.right);
            } else {
                res += "#,";
            }
        }
        return res;

    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        String[] values = data.split(",");
        int index = 0;
        TreeNode head = reconByLevel(values[index++]);
        Queue<TreeNode> queue = new LinkedList<>();
        if (head != null){
            queue.offer(head);
        }
        TreeNode node = null;
        while (!queue.isEmpty()){
            node = queue.poll();
            node.left = reconByLevel(values[index++]);
            node.right = reconByLevel(values[index++]);
            if (node.left != null){
                queue.offer(node.left);
            }
            if (node.right != null){
                queue.offer(node.right);
            }
        }
        return head;

    }

    public TreeNode reconByLevel(String str){
        if (str.equals("#")){
            return null;
        }
        return new TreeNode(Integer.valueOf(str));
    }
```

# 102、二叉树的层序遍历

```
public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> result = new ArrayList<>();

        if (root == null){
            return result;
        }
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        while (!queue.isEmpty()){
            int count = queue.size();
            List<Integer> list = new ArrayList<>();
            for (int i = 0; i < count; i++){
                TreeNode node = queue.poll();
                list.add(node.val);
                if (node.left != null){
                    queue.offer(node.left);
                }
                if (node.right != null){
                    queue.offer(node.right);
                }
            }
            result.add(list);
        }
        return result;
    }
```

# 404、左叶子之和

```
public int sum = 0;
    public int sumOfLeftLeaves(TreeNode root) {
        if (root == null){
            return sum;
        }
        if (root.left != null && root.left.left == null && root.left.right == null){
            sum += root.left.val;
        }
        sumOfLeftLeaves(root.left);
        sumOfLeftLeaves(root.right);
        return sum;

    }
```

# 面试题04.04、二叉树的平衡性

```
 public boolean isBalance = true;
    public boolean isBalanced(TreeNode root) {
        if (root == null){
            return true;
        }
        getHeight(root);
        return isBalance;

    }

    public int getHeight(TreeNode node){
        if (node == null){
            return 0;
        }
        if (!isBalance){
            return 0;
        }
        int left = getHeight(node.left);
        int right = getHeight(node.right);
        if (Math.abs(left-right) > 1){
            isBalance = false;
        }
        return Math.max(left, right) + 1;
    }
```

# 面试题04.05、验证二叉搜索树

```
public boolean isValidBST(TreeNode root) {
        Stack<TreeNode> stack = new Stack<>();
        double inorder = -Double.MAX_VALUE;

        while (!stack.isEmpty() || root != null){
            while (root != null){
                stack.push(root);
                root = root.left;
            }
            root = stack.pop();
            if (root.val <= inorder){
                return false;
            }
            inorder = root.val;
            root = root.right;
        }
        return true;
    }
```

# 面试题04.06、后继者

```
public TreeNode inorderSuccessor(TreeNode root, TreeNode p) {
        if (p.right != null){
            p = p.right;
            while (p.left != null){
                p = p.left;
            }
            return p;
        } else {
            TreeNode node = root;
            TreeNode res = null;
            while (p != node){
                if (p.val < node.val){
                    res = node;
                    node = node.left;
                } else {
                    node = node.right;
                }
            }
            return res;
        }
    }
```

# 面试题04.08、首个共同祖先

```
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || p == root || q == root){
            return root;
        }

        TreeNode leftNode = lowestCommonAncestor(root.left, p, q);
        TreeNode rightNode = lowestCommonAncestor(root.right, p, q);
        if (leftNode == null){
            return rightNode;
        } else if (rightNode == null){
            return leftNode;
        } else {
            return root;
        }
    }
```

# 530、二叉搜索树的最小绝对差

```
public int getMinimumDifference(TreeNode root) {
        Stack<TreeNode> stack = new Stack<>();
        List<Integer> result = new ArrayList<>();
        while (root != null || !stack.isEmpty()){
            while (root != null){
                stack.push(root);
                root = root.left;
            }
            if (!stack.isEmpty()){
                root = stack.pop();
                result.add(root.val);
                root = root.right;
            }
        }
        int min = Integer.MAX_VALUE;
        for (int i = 0; i < result.size() - 1; i++){
            if (min > result.get(i+1) - result.get(i)){
                min = result.get(i+1) - result.get(i);
            }
        }
        return min;

    }
```

# 525、连续数组

```
public int findMaxLength(int[] nums) {
        Map<Integer, Integer> map = new HashMap<>();
        map.put(0, -1);
        int maxLen = 0;
        int count = 0;
        for (int i = 0; i < nums.length; i++){
            count = count + (nums[i] > 0 ? 1:-1);
            if (map.containsKey(count)){
                maxLen = Math.max(maxLen, i - map.get(count));
            } else {
                map.put(count, i);
            }
        }
        return maxLen;

    }
```

# 面试题01.04、回文排列

```
public boolean canPermutePalindrome(String s) {
        Map<Character, Integer> map = new HashMap<>();
        for (int i = 0; i < s.length(); i++){
            if (map.containsKey(s.charAt(i))){
                int num = map.get(s.charAt(i));
                map.put(s.charAt(i), num+1);
            } else {
                map.put(s.charAt(i), 1);
            }
        }

        int count = 0;
        for (Character c: map.keySet()){
            if (map.get(c) % 2 != 0){
                count++;
            }
        }
        if (count > 1){
            return false;
        }
        return true;
    }
```

# 535、TinyURL的加密与解密

```
 Map<Integer, String> map = new HashMap<>();
    int i = 0;

    public String encode(String longUrl) {
        map.put(i, longUrl);
        return "http://tinyurl.com/" + i++;
    }

    public String decode(String shortUrl) {
        return map.get(Integer.parseInt(shortUrl.replace("http://tinyurl.com/", "")));
    }
```

# 36、有效的数独

```
 public boolean isValidSudoku(char[][] board) {
        for (int i = 0; i < 9; i++){
            Set<Character> row = new HashSet<>();
            Set<Character> col = new HashSet<>();
            for (int j = 0; j < 9; j++){
                if (row.contains(board[i][j]) || col.contains(board[j][i])){
                    return false;
                }
                if (board[i][j] != '.'){
                    row.add(board[i][j]);
                }
                if (board[j][i] != '.'){
                    col.add(board[j][i]);
                }
            }
        }
        Set<Character>[] box = new HashSet[9];
        for (int i = 0; i < 9; i++){
            box[i] = new HashSet<Character>();
        }
        for (int i = 0; i < 9; i++){
            for (int j = 0; j < 9; j++){
                int num = (i/3) * 3 + j/3;
                if (box[num].contains(board[i][j])){
                    return false;
                }
                if (board[i][j] != '.'){
                    box[num].add(board[i][j]);
                }
            }
        }
        return true;
    }
```

# 575、分糖果

```
public int distributeCandies(int[] candies) {
        if (candies == null || candies.length == 0)
            return 0;
        Set<Integer> set = new HashSet<Integer>();
        for (int i = 0; i < candies.length; i++){
            set.add(candies[i]);
        }
        if (set.size() <= candies.length/2){
            return set.size();
        }
        return candies.length/2;
    }
```

# 594、最长和谐子序列

```
 public int findLHS(int[] nums) {
        Arrays.sort(nums);
        int i = 0;
        int max = 0;
        while (i < nums.length - 1){
            int count = 0;
            int j = i+1;
            while (j < nums.length && nums[j] - nums[i] <= 1){
                j++;
            }
            if (nums[j-1] - nums[i] == 1){
                count = j-i;
                max = Math.max(max, count);
            }
            i++;
        }
        return max;
    }
```

# 520、检测大写字母

```
public boolean detectCapitalUse(String word) {
        int count = 0;
        int index = 0;
        int len = word.length();
        for (int i = 0; i < len; i++){
            if (word.charAt(i) >= 'A' && word.charAt(i) <= 'Z'){
                count++;
                index = i;
            }
        }
        if (count == len || count == 0){
            return true;
        }
        if (count == 1 && index == 0){
            return true;
        }
        return false;
    }
```

# 551、学生的出勤记录

```
public boolean checkRecord(String s) {
        char[] str = s.toCharArray();
        int absent = 0;
        int late = 0;
        for (int i = 0; i < str.length; i++){
            if (str[i] == 'P'){
                continue;
            } else if (str[i] == 'A'){
                absent++;
            } else {
                if (i+1 < str.length && i+2 < str.length && str[i+1] == 'L' && str[i+2] == 'L'){
                    late++;
                }
            }
        }
        if (absent > 1 || late >= 1){
            return false;
        }
        return true;
    }
```

# 1282、用户分组

```
public List<List<Integer>> groupThePeople(int[] groupSizes) {
        List<List<Integer>> result = new ArrayList<>();
        if (groupSizes.length == 0){
            return result;
        }

        Map<Integer, List<Integer>> map = new HashMap<>();

        for (int i = 0; i < groupSizes.length; i++){
            if (!map.containsKey(groupSizes[i])){
                map.put(groupSizes[i], new ArrayList<>());
            }

            List<Integer> sub = map.get(groupSizes[i]);
            sub.add(i);
            map.put(groupSizes[i], sub);
            if (sub.size() == groupSizes[i]){
                result.add(new ArrayList<>(sub));
                sub.clear();
            }
        }
        return result;
    }
```

# 1029、两地调度

```
public int twoCitySchedCost(int[][] costs) {
        if(costs.length == 0){
            return 0;
        }
        int sum = 0;
        int len = costs.length;
        int[] diatance = new int[len];
        for (int i = 0; i < costs.length; i++){
            sum += costs[i][0];
            diatance[i] = costs[i][1] - costs[i][0];
        }
        Arrays.sort(diatance);
        for (int i = 0; i < len / 2; i++){
            sum += diatance[i];
        }
        return sum;

    }
```

# 12、整数转罗马数字

```
public String intToRoman(int num) {
         int[] nums = {1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1};
        String[] romans = {"M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I"};

        int index = 0;
        StringBuilder sb = new StringBuilder();
        while (index < 13){
            while (num >= nums[index]){
                sb.append(romans[index]);
                num -= nums[index];
            }
            index++;
        }
        return sb.toString();

    }
```

# 1046、最后一块石头的重量

```
 public int lastStoneWeight(int[] stones) {
        PriorityQueue<Integer> queue = new PriorityQueue<>((o1, o2) -> (o2-o1));
        for (int i = 0; i < stones.length; i++){
            queue.add(stones[i]);
        }
        while (queue.size() > 1){
            int x = queue.poll();
            int y = queue.poll();
            if (x != y){
                queue.add(x-y);
            }
        }
        if (queue.size() == 0){
            return 0;
        } else {
            return queue.poll();
        }
    }
```

# 3、无重复的最长子串

```
public int lengthOfLongestSubstring(String s) {
        int n = s.length();
        int i = 0;
        int j = 0;
        int result = 0;
        Set<Character> set = new HashSet<>();
        while (i < n && j < n){
            if (!set.contains(s.charAt(j))){
                set.add(s.charAt(j));
                j++;
                result = Math.max(result, j-i);
            } else {
                set.remove(s.charAt(i));
                i++;
            }
        }
        return result;
    }
```

# 2、两数相加

```
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode pre = new ListNode(0);
        ListNode curr = pre;
        int carry = 0;
        while (l1 != null || l2 != null){
            int x = l1 == null ? 0 : l1.val;
            int y = l2 == null ? 0 : l2.val;
            int sum = x + y + carry;
            carry = sum / 10;
            sum = sum % 10;

            curr.next = new ListNode(sum);
            curr = curr.next;
            if (l1 != null){
                l1 = l1.next;
            }
            if (l2 != null){
                l2 = l2.next;
            }
        }
        if (carry != 0){
            curr.next = new ListNode(carry);
        }
        return pre.next;
    }
```

# 5、最长回文子串

```
public String longestPalindrome(String s) {
        if (s.length() < 2){
            return s;
        }
        int n = s.length();
        //代表s[i,j]是否是回文串
        boolean[][] dp = new boolean[n][n];
        for (int i = 0; i < n; i++){
            dp[i][i] = true;
        }
        int beginIndex = 0;
        int maxLen = 1;
        for (int j = 1; j < n; j++){
            for (int i = 0; i < j; i++){
                if (s.charAt(i) != s.charAt(j)){
                    dp[i][j] = false;
                } else {
                    if (j - i < 3){
                        dp[i][j] = true;
                    } else {
                        dp[i][j] = dp[i+1][j-1];
                    }
                }
                if (dp[i][j] && maxLen < j-i+1){
                    maxLen = j-i+1;
                    beginIndex = i;
                }

            }

        }
        return s.substring(beginIndex, beginIndex + maxLen);
    }
```

# 236、二叉树的最近公共祖先

```
 public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || p == root || q == root)
            return root;
        TreeNode left = lowestCommonAncestor(root.left, p,q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        if (left == null)
            return right;
        if (right == null)
            return left;
        return root;

    }
```

# 405、数字转换为十六进制数

```
public String toHex(int num) {
        StringBuffer sb = new StringBuffer();

        char[] array = "0123456789abcdef".toCharArray();
        if (num == 0)
            return "0";
        while (num != 0){
            int temp = num & 15;
            sb.append(array[temp]);
            num = num >>> 4;
        }
        return sb.reverse().toString();
    }
```

# 206、反转链表

```
public ListNode reverseList(ListNode head) {
        ListNode pre = null;
        ListNode curr = head;
        while (curr != null){
            ListNode temp = curr.next;
            curr.next = pre;
            pre = curr;
            curr = temp;
        }
        return pre;
    }
```

# 200、岛屿数量

```
public int numIslands(char[][] grid) {
        int rows = grid.length;
        if (rows == 0)
            return 0;
        int cols = grid[0].length;
        int result = 0;
        for (int i = 0; i < rows; i++){
            for (int j = 0; j < cols; j++){
                if (grid[i][j] == '1'){
                    result++;
                    dfs(grid, i ,j);
                }
            }
        }
        return result;
    }

    public void dfs(char[][] grid, int i, int j){
        int rows = grid.length;
        int cols = grid[0].length;

        if(i < 0 || i >= rows || j < 0 || j >= cols || grid[i][j] == '0')
            return ;
        grid[i][j] = '0';

        dfs(grid, i+1, j);
        dfs(grid, i-1, j);
        dfs(grid, i, j+1);
        dfs(grid, i, j-1);
    }
```

# 1、两数之和

```
 public int[] twoSum(int[] nums, int target) {
        if (nums.length < 2)
            return new int[]{};
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++){
            map.put(nums[i],i);
        }
        for (int i = 0; i < nums.length; i++){
            if (map.containsKey(target-nums[i]) && i != map.get(target-nums[i])){
                return new int[] {i, map.get(target-nums[i])};
            }
        }
        return new int[]{};
    }
```

# 4、寻找两个正序数组的中位数

// 采用归并排序的方式

```
public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int[] result = merge(nums1, nums2, nums1.length, nums2.length);
        int total = nums1.length + nums2.length;
        if (total % 2 == 1)
            return (double)result[total/2];
        else
            return (result[total/2-1] + result[total/2])/2.0;
    }

    public int[] merge(int[] nums1, int[] nums2, int len1, int len2){
        int[] help = new int[len1+len2];
        int i = 0;
        int j = 0;
        int k = 0;
        while (i < len1 && j < len2){
            if (nums1[i] <= nums2[j]){
                help[k] = nums1[i];
                i++;
                k++;
            } else {
                help[k] = nums2[j];
                j++;
                k++;
            }
        }
        while (i < len1){
            help[k++] = nums1[i++];
        }
        while (j < len2){
            help[k++] = nums2[j++];
        }
        return help;

    }
```

# 7、整数反转

```
 public int reverse(int x) {
        if (x == 0)
            return 0;
        boolean flag = x > 0 ? true : false;
        x = x > 0 ? x : -x;
        long result = 0;
        while (x != 0){
            long temp = x % 10;
            result = result * 10 + temp;
            x  = x / 10;

        }
        if (!flag){
            result = -result;
        }
        if (result > 1024*1024*1024*2-1 || result < -1024*1024*1024*2){
            return 0;
        } else{
            return (int)result;
        }
    }
```

# 9、回文数

```
public boolean isPalindrome(int x) {
        String xx = Integer.toString(x);
        int i = 0;
        int j = xx.length() - 1;
        while (i <= j && xx.charAt(i) == xx.charAt(j)){
            i++;
            j--;
            if (i >= xx.length() / 2)
                return true;
        }
        return false;
    }
```

# 12、整数转罗马数字

```
public String intToRoman(int num) {
         int[] nums = {1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1};
         String[] roman = {"M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V",
         "IV","I"};
         int index = 0;
         StringBuffer sb = new StringBuffer();
         while (index < 13){
             while (num >= nums[index]){
                 sb.append(roman[index]);
                 num -= nums[index];
             }
             index++;
         }
         return sb.toString();

    }
```

# 面试题16.11、跳水板

```
public int[] divingBoard(int shorter, int longer, int k) {
        if (k == 0){
            return new int[]{};
        }
        Set<Integer> set = new HashSet<>();
        for (int i = 0; i <= k; i++){
            int temp = k - i;
            set.add(shorter * i + longer * temp);
        }
        Object[] obj = set.toArray();
        int[] result = new int[obj.length];
        for (int i = 0; i < obj.length; i++){
            result[i] = (int)obj[i];
        }
        Arrays.sort(result);
        return result;

    }
```

# 面试题02.04、分割链表

```
public ListNode partition(ListNode head, int x) {
        if (head == null || head.next == null)
            return head;
        ListNode preleft = new ListNode(0);
        ListNode preright = new ListNode(0);
        ListNode currleft = preleft;
        ListNode currright = preright;
        while (head != null){
            if (head.val < x){
                ListNode temp = head.next;
                head.next = null;
                currleft.next = head;
                currleft = head;
                head = temp;
            } else {
                ListNode temp = head.next;
                head.next = null;
                currright.next = head;
                currright = head;
                head = temp;
            }
        }
        currleft.next = preright.next;
        return preleft.next;
    }
```

# 面试题02.02、返回倒数第k个节点

```
public int kthToLast(ListNode head, int k) {
        if(head == null)
            return -1;
        ListNode slow = head;
        ListNode fast = head;
        int count = 1;
        while (count <= k){
            fast = fast.next;
            count++;
            if (fast == null)
                return head.val;
        }
        while (fast != null){
            fast = fast.next;
            slow = slow.next;
        }
        return slow.val;
    }
```

# 面试题02.07、链表相交

```
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if (headA == null || headB ==null)
            return null;
        ListNode a = headA;
        ListNode b = headB;
        int countA = 0;
        int countB = 0;
        while (a != null){
            countA++;
            a = a.next;
        }
        while (b != null){
            countB++;
            b = b.next;
        }
        a = headA;
        b = headB;
        if (countA >= countB){
            int x = countA - countB;
            int i = 1;
            while (i <= x){
                a = a.next;
                i++;
            }
            while (a != b){
                a = a.next;
                b = b.next;
            }
            return a;
        } else {
            int x = countB - countA;
            int i = 1;
            while (i <= x){
                b = b.next;
                i++;
            }
            while (a != b){
                a = a.next;
                b = b.next;
            }
            return b;
        }
    }
```

# 26、删除排序数组中的重复项

```
public int removeDuplicates(int[] nums) {
        if (nums.length == 0)
            return 0;
        int i = 0;
        int j = 0;
        while (j < nums.length){
            if (j > 0 && nums[j] == nums[j-1]){
                j++;
            } else {
                nums[i++] = nums[j++];
            }
        }
        return i;
    }
```

# 27、移除元素

```
public int removeElement(int[] nums, int val) {
        if (nums.length == 0)
            return 0;
        int count = 0;
        int i = 0;
        int j = 0;
        while (j < nums.length){
            if (nums[j] == val){
                count++;
            } else {
                nums[i-count] = nums[j];
            }
            i++;
            j++;
        }
        return nums.length - count;
    }
```

# 28、实现strStr()

```
public int strStr(String haystack, String needle) {
        int len1 = haystack.length();
        int len2 = needle.length();
        if (len1 == 0 && len2 == 0)
            return 0;
        if (len1 < len2)
            return -1;
        if (len2 == 0)
            return 0;
        char[] hay = haystack.toCharArray();
        char[] need = needle.toCharArray();
        int i = 0;
        int j = 0;
        while (i < len1){
            int k = i;
            if (hay[i] != need[j]){
                i++;
            } else {
                while (i < len1 && j < len2 && hay[i] == need[j]){
                    i++;
                    j++;
                }
                if (j == len2){
                    return i-j;
                } else {
                    i = k+1;
                    j = 0;
                }
            }
        }
        return -1;
    }
```

# 61、旋转链表

```
 public ListNode rotateRight(ListNode head, int k) {
        if (head == null)
            return null;
        if (k == 0)
            return head;
        ListNode slow = head;
        ListNode fast = head;
        ListNode cal = head;
        int sum = 0;
        while (cal != null){
            cal = cal.next;
            sum++;
        }
        k = k % sum;
        if (k == 0)
            return head;
        int count = 1;
        while (fast != null && count <= k){
            fast = fast.next;
            count++;
            if (fast == null)
                return null;
        }
        while (fast.next != null){
            slow = slow.next;
            fast = fast.next;
        }
        ListNode result = slow.next;
        ListNode temp = result;
        slow.next = null;
        while (temp != null && temp.next != null){
            temp = temp.next;
        }
        temp.next = head;
        return result;
    }
```

# 36、有效的数独

```
public boolean isValidSudoku(char[][] board) {
        for (int i = 0; i < 9; i++){
            Set<Character> row = new HashSet<>();
            Set<Character> col = new HashSet<>();
            for (int j = 0; j < 9; j++){
                if (row.contains(board[i][j]) || col.contains(board[j][i]))
                    return false;
                if (board[i][j] != '.'){
                    row.add(board[i][j]);
                }
                if (board[j][i] != '.'){
                    col.add(board[j][i]);
                }
            }
        }
        Set<Character>[] result = new HashSet[9];
        for (int i = 0; i < 9; i++){
            result[i] = new HashSet<Character>();
        }
        for (int i = 0; i < 9; i++){
            for (int j = 0; j < 9; j++){
                int temp = (i/3)*3 + j/3;
                if (result[temp].contains(board[i][j]))
                    return false;
                if (board[i][j] != '.'){
                    result[temp].add(board[i][j]);
                }
            }
        }
        return true;
    }
```

# 25、k个一组翻转链表

```
public ListNode reverseKGroup(ListNode head, int k) {
        if (head == null)
            return null;
        ListNode pre = head;
        int count = 1;
        while (pre.next != null){
            pre = pre.next;
            count++;
        }
        int group = count / k;
        pre = head;
        ListNode result = new ListNode(0);
        ListNode curr = result;
        for (int i = 0; i < group; i++){
            int temp = 0;
            Stack<ListNode> stack = new Stack<>();
            while (temp < k){
                ListNode node = pre.next;
                pre.next = null;
                stack.push(pre);
                pre = node;
                temp++;
            }
            while (!stack.isEmpty()){
                ListNode p = stack.pop();
                curr.next = p;
                curr = p;
            }
        }
        if (pre != null){
            curr.next = pre;
        }
        return result.next;
    }
```

# 120、三角形最小路径和

```
public int minimumTotal(List<List<Integer>> triangle) {
        int rows = triangle.size();
        int[][] dp = new int[rows][rows];
        dp[0][0] = triangle.get(0).get(0);
        for (int i = 1; i < rows; i++){
            List<Integer> temp = triangle.get(i);
            for (int j = 0; j < temp.size(); j++){
                if (j == 0){
                    dp[i][j] = dp[i-1][j] + temp.get(j);
                } else if (j == temp.size() - 1){
                    dp[i][j] = dp[i-1][j-1] + temp.get(j);
                } else {
                    dp[i][j] = Math.min(dp[i-1][j-1], dp[i-1][j]) + temp.get(j);
                }
            }
        }
        int min = Integer.MAX_VALUE;
        for (int i = 0; i < rows; i++){
            min = Math.min(min, dp[rows-1][i]);
        }
        return min;

    }
```

# 111、二叉树的最小深度

```
public int minDepth(TreeNode root) {
        if (root == null)
            return 0;
        int left = minDepth(root.left);
        int right = minDepth(root.right);
        if (root.left == null || root.right == null){
            return left + right + 1;
        } else {
            return Math.min(left, right) + 1;
        }
    }
```

# 129、求根节点到叶子节点数字之和

```
 List<String> result = new ArrayList<>();
    public int sumNumbers(TreeNode root) {
        if (root == null)
            return 0;
        backtrack(root, new StringBuffer());
        int sum = 0;
        for (int i = 0; i < result.size(); i++){
            sum += Integer.valueOf(result.get(i));
        }
        return sum;
    }
    public void backtrack(TreeNode root, StringBuffer sb){
        if (root == null)
            return;
        sb.append(root.val + "");
        if (root.left == null && root.right == null){
            result.add(sb.toString());
        }
        backtrack(root.left, sb);
        backtrack(root.right, sb);
        sb.deleteCharAt(sb.length()-1);
    }
```

# 137、只出现一次的数字II

```
public int singleNumber(int[] nums) {
        Map<Integer, Integer> map = new HashMap<Integer, Integer>();
        for (int i = 0; i < nums.length; i++){
            if (!map.containsKey(nums[i])){
                map.put(nums[i], 1);
            } else {
                int count = map.get(nums[i]);
                map.put(nums[i],count+1);
            }
        }
        for (Map.Entry<Integer, Integer> entry: map.entrySet()){
            if (entry.getValue() == 1){
                return entry.getKey();
            }
        }
        return -1;
    }
```

# 155、最小栈

```
class MinStack {
    Stack<Integer> stack = new Stack<>();
    Stack<Integer> minStack = new Stack<>();
    /** initialize your data structure here. */
    public MinStack() {

    }

    public void push(int x) {
        stack.push(x);
        if (minStack.isEmpty()){
            minStack.push(x);
        } else {
            if (stack.peek() < minStack.peek()){
                minStack.push(stack.peek());
            } else {
                minStack.push(minStack.peek());
            }
        }
    }

    public void pop() {
        if (!stack.isEmpty())
            stack.pop();
        if (!minStack.isEmpty())
            minStack.pop();
    }

    public int top() {
        return stack.peek();
    }

    public int getMin() {
        return minStack.peek();
    }
}
```

# 234、回文链表

```
 public boolean isPalindrome(ListNode head) {
        if (head == null)
            return true;
        ListNode node = head;
        int count = 1;
        while (node.next != null){
            node = node.next;
            count++;
        }
        node = head;
        int half = count / 2;
        Stack<ListNode> stack = new Stack<>();
        int i = 0;
        while (i < half){
            stack.push(node);
            node = node.next;
            i++;
        }
        if (count % 2 == 1){
            node = node.next;
        }
        while (!stack.isEmpty() && node != null){
            ListNode temp = stack.pop();
            if (temp.val != node.val)
                return false;
            node = node.next;
        }
        return true;

    }
```

# 215、数组中的第k个最大元素

```
public int findKthLargest(int[] nums, int k) {
        PriorityQueue<Integer> queue = new PriorityQueue<>();
        if (nums.length < k)
            return -1;
        for (int i = 0; i < nums.length; i++){
            queue.add(nums[i]);
            if (queue.size() > k){
                queue.poll();
            }
        }
        return queue.poll();

    }
```

# 23、合并k个排序链表

```
public ListNode mergeKLists(ListNode[] lists) {
       PriorityQueue<ListNode> queue = new PriorityQueue<>((o1, o2) -> (o1.val-o2.val));
       if (lists == null){
           return null;
       }
       if (lists.length == 1){
           return lists[0];
       }
       for (int i = 0; i < lists.length; i++){
           if (lists[i] != null){
               queue.offer(lists[i]);
           }
       }

       ListNode pre = new ListNode(0);
       ListNode curr = pre;
       while (!queue.isEmpty()){
           ListNode temp = queue.poll();
           curr.next = temp;
           curr = temp;
           if (temp.next != null){
               queue.offer(temp.next);
           }
       }
       return pre.next;
    }
```

# 33、搜索旋转排序数组

```
public int search(int[] nums, int target) {
        if (nums.length == 0)
            return -1;
        int left = 0;
        int right = nums.length - 1;
        while (left <= right){
            int mid = left + (right-left)/2;
            if (nums[mid] == target)
                return mid;
            if (nums[left] <= nums[mid]){
                if (target >= nums[left] && target < nums[mid]){
                    right = mid-1;
                } else {
                    left = mid+1;
                }
            } else {
                if (target > nums[mid] && target <= nums[right]){
                    left = mid+1;
                } else {
                    right = mid-1;
                }
            }
        }
        return -1;
    }
```

# 35、搜索插入位置

```
public int searchInsert(int[] nums, int target) {
        if (nums.length == 0)
            return 0;
        int left = 0;
        int right = nums.length - 1;
        while (left <= right){
            int mid = left + (right - left) /2;
            if (nums[mid] == target){
                return mid;
            } else if (nums[mid] < target){
                left = mid + 1;
            } else {
                right = mid - 1;
            }

        }
        return left;
    }
```

# 103、二叉树的锯齿形层次遍历

```
 public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        List<List<Integer>> result = new ArrayList<>();
        if (root == null)
            return result;
        Queue<TreeNode> queue = new LinkedList<>();
        TreeNode node = root;
        int depth = 1;
        queue.offer(root);
        while (!queue.isEmpty()) {
            ArrayList<Integer> temp = new ArrayList<>();
            if (depth % 2 == 1){
                int len = queue.size();
                for (int i = 0; i < len; i++){
                    TreeNode t = queue.poll();
                    temp.add(t.val);
                    if (t.left != null)
                        queue.offer(t.left);
                    if (t.right != null)
                        queue.offer(t.right);
                }
                result.add(temp);
                depth++;
            } else {
                int len = queue.size();
                Stack<TreeNode> stack = new Stack<>();
                for (int i = 0; i < len; i++){
                    TreeNode t = queue.poll();
                    stack.push(t);
                    if (i == len-1){
                        while (!stack.isEmpty()) {
                            temp.add(stack.pop().val);
                        }
                    }
                    if (t.left != null)
                        queue.offer(t.left);
                    if (t.right != null)
                        queue.offer(t.right);
                }
                result.add(temp);
                depth++;
            }
        }
        return result;
    }
```

# 108、将有序数组转换为二叉平衡树

```
public TreeNode sortedArrayToBST(int[] nums) {
        if (nums.length == 0)
            return null;
        if (nums.length == 1)
            return new TreeNode(nums[0]);
        int len = nums.length;
        TreeNode root = new TreeNode(nums[len/2]);
        int[] left = Arrays.copyOfRange(nums, 0, len/2);
        int[] right = Arrays.copyOfRange(nums, len/2+1, len);
        root.left = sortedArrayToBST(left);
        root.right = sortedArrayToBST(right);
        return root;
    }
```

# 109、有序链表转化为二叉搜索树

```
public TreeNode sortedListToBST(ListNode head) {
        if (head == null)
            return null;
        if (head.next == null)
            return new TreeNode(head.val);
        ListNode node = head;
        ListNode fast = head;
        ListNode slow = head;
        ListNode preSlow = null;
        while (fast != null && fast.next != null){
            preSlow = slow;
            slow = slow.next;
            fast = fast.next.next;
        }
        TreeNode root = new TreeNode(slow.val);
        preSlow.next = null;
        root.left = sortedListToBST(head);
        root.right = sortedListToBST(slow.next);
        return root;

    }
```

# 148、排序链表

```
public ListNode sortList(ListNode head) {
        if (head == null || head.next == null)
            return head;
        PriorityQueue<ListNode> queue = new PriorityQueue<>((o1, o2) -> (o1.val - o2.val));
        ListNode node = head;
        while (node != null){
            ListNode t = node.next;
            node.next = null;
            queue.add(node);
            node = t;
        }
        ListNode pre = new ListNode(0);
        ListNode curr = pre;
        while (!queue.isEmpty()){
            ListNode temp = queue.poll();
            curr.next = temp;
            curr = temp;
        }
        return pre.next;
    }
```

# 150、逆波兰表达式求值

```
public int evalRPN(String[] tokens) {
        if (tokens.length == 0)
            return -1;
        Stack<Integer> stack = new Stack<>();
        int one = 0;
        int two = 0;
        for (int i = 0; i < tokens.length; i++){
            switch(tokens[i]){
                case "+":
                    two = stack.pop();
                    one = stack.pop();
                    stack.push(two + one);
                    break;
                case "-":
                    two = stack.pop();
                    one = stack.pop();
                    stack.push(one-two);
                    break;
                case "/":
                    two = stack.pop();
                    one = stack.pop();
                    stack.push(one/two);
                    break;
                case "*":
                    two = stack.pop();
                    one = stack.pop();
                    stack.push(one * two);
                    break;
                default:
                    stack.push(Integer.valueOf(tokens[i]));

            }
        }
        return stack.pop();
    }
```

# 1025、除数博弈

```
public boolean divisorGame(int N) {
        boolean[] dp = new boolean[N+1];
        if (N == 1)
            return false;
        if (N == 2)
            return true;
        dp[1] = false;
        dp[2] = true;
        for (int i = 3; i <= N; i++){
            for (int j = 1; j < i; j++){
                if (i % j == 0 && !dp[i-j]){
                    dp[i] = true;
                    break;
                }
            }
        }
        return dp[N];
    }
```

# 60、第k个排列

```
List<String> result = new ArrayList<>();
    public String getPermutation(int n, int k) {
        if (k <= 0)
            return "";
        backtrack(n, k, new boolean[n], new StringBuffer());
        return result.get(k-1);
    }
    public void backtrack(int n, int k, boolean[] isVisited, StringBuffer sb) {
        if (result.size() == k)
            return;
        if (sb.length() == n){
            result.add(sb.toString());
            return;
        }
        for (int i = 0; i < n; i++){
            if (isVisited[i])
                continue;
            isVisited[i] = true;
            sb.append(i+1);
            backtrack(n ,k, isVisited, sb);
            isVisited[i] = false;
            sb.deleteCharAt(sb.length()-1);
        }   
    }
```

# 48、旋转图像

```
 public void rotate(int[][] matrix) {
        if (matrix.length == 0)
            return;
        int n = matrix.length;
        int top = 0;
        int left = 0;
        int bottom = matrix.length - 1;
        int right = matrix[0].length - 1;
        while (left < right) {
            singleRotate(matrix, left, right, top, bottom);
            left++;
            top++;
            right--;
            bottom--;
        }
    }

    public void singleRotate(int[][] matrix, int left, int right, int top, int bottom){
        int times = right - left;
        for (int i = 0; i < times; i++){
            int temp = matrix[top][left+i];
            matrix[top][left+i] = matrix[bottom-i][left];
            matrix[bottom-i][left] = matrix[bottom][right-i];
            matrix[bottom][right-i] = matrix[top+i][right];
            matrix[top+i][right] = temp;
        }
    }
```

# 63、不同路径II

```
public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        if (obstacleGrid.length == 0)
            return 0;
        int rows = obstacleGrid.length;
        int cols = obstacleGrid[0].length;
        int[][] dp = new int[rows][cols];
        for (int i = 0; i < cols && obstacleGrid[0][i] == 0; i++){
            dp[0][i] = 1;
        }
        for (int i = 0; i < rows && obstacleGrid[i][0] == 0; i++){
            dp[i][0] = 1;
        }
        for (int i = 1; i < rows; i++){
            for (int j = 1; j < cols; j++){
                if (obstacleGrid[i][j] == 0){
                    dp[i][j] = dp[i-1][j] + dp[i][j-1];
                }
            }
        }
        return dp[rows-1][cols-1];
    }
```

# 66、加一

```
public int[] plusOne(int[] digits) {
        if (digits.length == 0){
            return new int[]{};
        }
        int carry = 0;
        for (int i = digits.length - 1; i >= 0; i--){
            digits[i]++;
            if (digits[i] < 10)
                return digits;
            else {
                digits[i] = digits[i] % 10;
            }
        }
        digits = new int[digits.length+1];
        digits[0] = 1;
        return digits;
    }
```

# 67、二进制求和

```
 public String addBinary(String a, String b) {
        if (a.length() == 0)
            return b;
        if (b.length() == 0)
            return a;
        char[] a1 = a.toCharArray();
        char[] b1 = b.toCharArray();
        StringBuffer sb = new StringBuffer();
        int i = a1.length - 1;
        int j = b1.length - 1;
        int carry = 0;
        while (i >= 0 || j >= 0) {
            int x = (i < 0 ? 0 : a1[i]-'0');
            int y = (j < 0 ? 0 : b1[j]-'0');
            int temp = x + y + carry;
            carry = temp / 2;
            temp = temp % 2;
            sb.append(temp);
            i--;
            j--;
        }
        if (carry != 0){
            sb.append(carry);
        }
        return sb.reverse().toString();
    }
```

# 69、x的平方根（采用二分查找的方式）

```
public int mySqrt(int x) {
        int left = 0;
        int right = x;
        int result = 0;
        while (left <= right){
            int mid = left + (right - left) /2;
            if ((long)mid * mid <= x){
                result = mid;
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return result;
    }
```

# 73、矩阵置0

```
public void setZeroes(int[][] matrix) {
        if (matrix.length == 0)
            return;
        int rows = matrix.length;
        int cols = matrix[0].length;
        Set<Integer> rowSet = new HashSet<>();
        Set<Integer> colSet = new HashSet<>();
        for (int i = 0; i < rows; i++){
            for (int j = 0; j < cols; j++){
                if (matrix[i][j] == 0){
                    rowSet.add(i);
                    colSet.add(j);
                }
            }
        }
        for (int i = 0; i < rows; i++){
            for (int j = 0; j < cols; j++){
                if (rowSet.contains(i) || colSet.contains(j)){
                    matrix[i][j] = 0;
                }
            }
        }
    }
```

# 75、颜色分类

```
public void sortColors(int[] nums) {
        if (nums.length == 0)
            return;
        int p0 = 0;
        int curr = 0;
        int p2 = nums.length - 1;
        while (curr <= p2){
            int temp;
            if (nums[curr] == 0){
                temp = nums[curr];
                nums[curr] = nums[p0];
                nums[p0] = temp;
                p0++;
                curr++;
            } else if (nums[curr] == 2){
                temp = nums[curr];
                nums[curr] = nums[p2];
                nums[p2] = temp;
                p2--;
            } else {
                curr++;
            }
        }
    }
```

# 77、组合

```
List<List<Integer>> result = new ArrayList<>();
    public List<List<Integer>> combine(int n, int k) {
        if (n == 0 || k > n)
            return result;
        backtrack(new ArrayList<>(), n, k, 1);
        return result;
    }

    public void backtrack(List<Integer> temp, int n, int k, int start){
        if (temp.size() == k){
            result.add(new ArrayList<>(temp));
            return;
        }

        for (int i = start; i <= n; i++){
            temp.add(i);
            backtrack(temp, n, k, i+1);
            temp.remove(temp.size()-1);
        }

    }
```

# 78、子集

```
public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        if (nums == null || nums.length == 0)
            return result;
        backtrack(result, new ArrayList<>(), nums, 0);
        return result;
    }

    public void backtrack(List<List<Integer>> result, List<Integer> temp, int[] nums, int start){
        result.add(new ArrayList<>(temp));
        for (int i = start; i < nums.length; i++){
            temp.add(nums[i]);
            backtrack(result, temp, nums, i+1);
            temp.remove(temp.size() - 1);
        }
    }
```

# 79、单词搜索

```
int rows,cols;
    boolean[][] isVisited;
    int[][] d = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
    public boolean exist(char[][] board, String word) {
        if (board.length == 0 || word == "")
            return false;
        rows = board.length;
        cols = board[0].length;
        isVisited = new boolean[rows][cols];
        for (int i = 0; i < rows; i++){
            for (int j = 0; j < cols; j++){
                if (backtrack(board, i, j, isVisited, word, 0)){
                    return true;
                }
            }
        }
        return false;
    }
    public boolean backtrack(char[][] board, int i, int j, boolean[][] isVisited, String word, int index){
        if (index == word.length() - 1){
            return board[i][j] == word.charAt(index);
        }
        if (board[i][j] == word.charAt(index)){
            isVisited[i][j] = true;
            for (int k = 0; k < 4; k++){
                int x = i + d[k][0];
                int y = j + d[k][1];
                if (isLegal(x, y) && !isVisited[x][y] && backtrack(board, x, y, isVisited, word, index+1)){
                    return true;
                }
            }
            isVisited[i][j] = false;
        }
        return false;
    }

    public boolean isLegal(int i, int j){
        return i >= 0 && i < rows && j >= 0 && j < cols;
    }
```

# 面试题16.19、水域大小

```
int pondSize;
    int rows, cols;
    int[][] d = {{0, 1}, {0, -1}, {1, 0}, {-1, 0}, {1, 1}, {1, -1}, {-1, 1}, {-1, -1}};
    public int[] pondSizes(int[][] land) {
        if (land.length == 0)
            return new int[]{};
        rows = land.length;
        cols = land[0].length;
        List<Integer> list = new ArrayList<>();
        for (int i = 0; i < rows; i++){
            for (int j = 0; j < cols; j++){
                if (land[i][j] == 0){
                    pondSize = 0;
                    dfs(land, i, j);
                    list.add(pondSize);
                }
            }
        }
        int[] result = new int[list.size()];
        for (int i = 0; i < list.size(); i++){
            result[i] = list.get(i);
        }
        Arrays.sort(result);
        return result;
    }

    public void dfs(int[][] land, int i, int j){
        pondSize++;
        land[i][j] = -1;
        for (int k = 0; k < 8; k++){
            int x = i + d[k][0];
            int y = j + d[k][1];
            if (x >= 0 && x < rows && y >= 0 && y < cols && land[x][y] == 0){
                dfs(land, x, y);
            }
        }
    }
```

# 114、二叉树展开为链表

```
public void flatten(TreeNode root) {
        if (root == null)
            return;
        List<TreeNode> list = new ArrayList<>();
        list = pre(root, list);
        TreeNode pre = new TreeNode(0);
        TreeNode curr = pre;
        for (int i = 0; i < list.size(); i++){
            curr.right = list.get(i);
            curr.left = null;
            curr = curr.right;
        }

    }

    public List<TreeNode> pre(TreeNode root, List<TreeNode> list){
        if (root != null){
            list.add(root);
            pre(root.left, list);
            pre(root.right, list);
        }
        return list;
    }
```

# 81、搜索旋转排序数组II

```
public boolean search(int[] nums, int target) {
        if (nums == null || nums.length == 0)
            return false;
        int left = 0;
        int right = nums.length - 1;
        while (left <= right){
            int mid = left + (right-left)/2;
            if (nums[mid] == target)
                return true;
            if (nums[left] == nums[mid]){
                left++;
                continue;
            } else if (nums[left] <= nums[mid]){
                if (target >= nums[left] && target < nums[mid]){
                    right = mid - 1;
                } else {
                    left = mid + 1;
                }
            } else {
                if (target > nums[mid] && target <= nums[right]){
                    left = mid + 1;
                } else {
                    right = mid - 1;
                }
            }
        }
        return false;
    }
```

# 82、删除链表中重复元素II

```
public ListNode deleteDuplicates(ListNode head) {
        if (head == null)
            return null;
        ListNode pre = new ListNode(0);
        ListNode curr = pre;
        while (head != null){
            int temp = head.val;
            if (head.next != null && head.next.val == temp){
                while (head.next != null && head.next.val == temp){
                    head = head.next;
                }
                head = head.next;
            } else {
                ListNode node = head.next;
                head.next = null;
                curr.next = head;
                curr = head;
                head = node;
            }
        }
        return pre.next;
    }
```

# 83、删除链表中的重复元素

```
public ListNode deleteDuplicates(ListNode head) {
        if (head == null){
            return null;
        }
        ListNode pre = head;
        ListNode curr = head.next;
        pre.next = null;
        while (curr != null){
            if (curr.val == pre.val){
                curr = curr.next;
            } else {
                ListNode temp = curr.next;
                curr.next = null;
                pre.next = curr;
                pre = curr;
                curr = temp;
            }
        }
        return head;
    }
```

# 72、编辑距离

```
public int minDistance(String word1, String word2) {
        int len1 = word1.length();
        int len2 = word2.length();
        if (len1 == 0 || len2 == 0){
            return len1+len2;
        }
        int[][] dp = new int[len1+1][len2+1];
        dp[0][0] = 0;
        for (int i = 1; i <= len1; i++){
            dp[i][0] = dp[i-1][0] + 1;
        }
        for (int i = 1; i <= len2; i++){
            dp[0][i] = dp[0][i-1] + 1;
        }
        for (int i = 1; i <= len1; i++){
            for (int j = 1; j <= len2; j++){
                if (word1.charAt(i-1) == word2.charAt(j-1)){
                    dp[i][j] = dp[i-1][j-1];
                } else {
                    dp[i][j] = Math.min(dp[i-1][j-1], Math.min(dp[i-1][j], dp[i][j-1])) + 1;
                }
            }
        }
        return dp[len1][len2];
    }
```

# 92、反转链表II

```
public ListNode reverseBetween(ListNode head, int m, int n) {
        if (head == null)
            return null;
        if (n < m)
            return null;
        ListNode pre = new ListNode(0);
        ListNode curr = pre;
        curr.next = head;
        int count = 0;
        while (count < m - 1){
            curr = curr.next;
            count++;
        }
        ListNode temp = curr.next;
        curr.next = null;
        Stack<ListNode> stack = new Stack<>();
        while (count < n){
            ListNode listNode = temp.next;
            temp.next = null;
            stack.push(temp);
            temp = listNode;
            count++;
        }
        while (!stack.isEmpty()){
            ListNode l = stack.pop();
            curr.next = l;
            curr = l;
        }
        curr.next = temp;
        return pre.next;

    }
```

# 90、子集II

```
List<List<Integer>> result = new ArrayList<>();
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        if (nums.length == 0){
            return result;
        }
        Arrays.sort(nums);
        backtrack(nums, new ArrayList<>(), 0);
        return result;
    }

    public void backtrack(int[] nums, List<Integer> temp, int start){
        result.add(new ArrayList<>(temp));
        for (int i = start; i < nums.length; i++){
            if (i > start && nums[i] == nums[i-1])
                continue;
            temp.add(nums[i]);
            backtrack(nums, temp, i+1);
            temp.remove(temp.size() - 1);
        }
    }
```

# 232、用栈实现队列

```
Stack<Integer> stack1 = new Stack<>();
    Stack<Integer> stack2 = new Stack<>();
    int front;
    /** Initialize your data structure here. */
    public MyQueue() {

    }

    /** Push element x to the back of queue. */
    public void push(int x) {
        if (stack1.isEmpty()){
            front = x;
        }
        stack1.push(x);
    }

    /** Removes the element from in front of queue and returns that element. */
    public int pop() {
        if (stack2.isEmpty()){
            while (!stack1.isEmpty()){
                stack2.push(stack1.pop());
            }
        }
        if (!stack2.isEmpty()){
            return stack2.pop();
        }
        return -1;
    }

    /** Get the front element. */
    public int peek() {
        if (!stack2.isEmpty()){
            return stack2.peek();
        }
        return front;
    }

    /** Returns whether the queue is empty. */
    public boolean empty() {
        return stack1.isEmpty() && stack2.isEmpty();
    }
```

# 872、叶子相似的树

```
public boolean leafSimilar(TreeNode root1, TreeNode root2) {
        List<Integer> list1 = new ArrayList<>();
        List<Integer> list2 = new ArrayList<>();
        dfs(root1, list1);
        dfs(root2, list2);
        return list1.equals(list2);

    }

    public void dfs(TreeNode root, List<Integer> result){
        if (root != null){
            if (root.left == null && root.right == null){
                result.add(root.val);
            }
            dfs(root.left, result);
            dfs(root.right, result);
        }
    }
```

# 1576、替换所有的问号

```
public String modifyString(String s) {
        if (s.length() == 0)
            return "";
        char[] array = ("0" + s + "0").toCharArray();
        for (int i = 1; i < array.length-1; i++){
            if (array[i] == '?'){
                char left = array[i-1];
                char right = array[i+1];
                char temp = 'a';
                while (temp == left || temp == right){
                    temp++;
                }
                array[i] = temp;
            }
        }
        StringBuffer sb = new StringBuffer();
        for (int i = 1; i < array.length-1; i++){
            sb.append(array[i]);
        }
        return sb.toString();
    }
```

# 华为笔试题2

```
给N个节点，输入所在高度，最后二叉树有多少种？
public static int GetCNM(int n,  int m)
    {
        if(m == n)
            return 1;
        int pn = 1;
        int pm = 1;
        int pmn = 1;
        for(int i = 1; i <= n; ++ i)
        {
            pn *= i;
            if(i <= m) pm *= i;
            if(i <= n - m) pmn *= i;
        }

        return pn / (pm * pmn);
    }
    public static int GetTotal(int[] number){
        Arrays.sort(number);
        int len = number.length;
        int count  = 1;
        int preCount = 1;
        int val = number[0];


        int res = 1;
        for(int i = 1; i < len; ++ i)
        {
            if(number[i] == val)
            {
                count += 1;
            }else{
                res *= GetCNM(preCount, count);
                preCount = count * 2;
                count = 1;
                val = number[i];
            }
        }
        res *= GetCNM(preCount, count);
        return res;
    }
```

# 面试题01.06、字符串压缩

```
public String compressString(String S) {
        int len = S.length();
        if (len == 0)
            return "";
        char[] array = S.toCharArray();
        int i = 0;
        StringBuffer sb = new StringBuffer();
        while (i < len){
            int count = 1;
            int j = i+1;
            while (j < len && array[j] == array[i]){
                count++;
                j++;
            }
            sb.append(array[i]);
            sb.append(count);
            i = j;
        }
        if (sb.length() >= len){
            return S;
        }
        return sb.toString();
    }
```

# 1143、最长公共子序列

```
public int longestCommonSubsequence(String text1, String text2) {
        int m = text1.length();
        int n = text2.length();
        int[][] dp = new int[m+1][n+1];

        for (int i = 0; i < m; i++){
            for (int j = 0; j < n; j++){
                char c1 = text1.charAt(i);
                char c2 = text2.charAt(j);
                if (c1 == c2){
                    dp[i+1][j+1] = dp[i][j] + 1;
                } else {
                    dp[i+1][j+1] = Math.max(dp[i][j+1], dp[i+1][j]);
                }
            }
        }
        return dp[m][n];

    }
```

# 917、仅仅反转字母

```
public String reverseOnlyLetters(String S) {
        if (S.length() == 0)
            return "";
        char[] array = S.toCharArray();
        int len = array.length;
        Stack<Character> stack = new Stack<>();
        for (int i = 0; i < len; i++){
            if (Character.isLetter(array[i])){
                stack.push(array[i]);
            }
        }
        StringBuffer sb = new StringBuffer();
        for (int i = 0; i < len; i++){
            if (Character.isLetter(array[i])){
                sb.append(stack.pop());
            } else {
                sb.append(array[i]);
            }
        }
        return sb.toString();
    }
```

# 459、重复的子字符串

```
public boolean repeatedSubstringPattern(String s) {
        int len = s.length();
        for (int i = 1; i < len; i++){
            if (len % i == 0){
                String s1 = s.substring(0, i);
                boolean flag = true;
                for (int j = i; j+i <= len; j+=i){
                    if (!s1.equals(s.substring(j, j+i))){
                        flag = false;
                        break;
                    }
                }
                if (flag)
                    return true;
            }
        }
        return false;
    }
```
