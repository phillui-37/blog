> timestamp: 2023-04-26T12:03:00+08:00
> tag: backtracking, Leetcode
> category: Leetcode
> author: Phil Lui
> title: Leetcode#46 筆記


46是一條非常典型的backtracking題目,個人思路為兩層iteration加上recursion,這做法不是最快但也足夠

1. Iterate param (`vector<int>`) 每一個entry
2. 在每一回的iterate中把除當事entry外重組新vector, 然後以新vector recursive call自己
3. 獲得call return後iterate result, 把最外層entry放在每一個result的begin,然後push到return list

基本上是直接做下去，也不用想太多algothrim上的問題，optimize的話就用swap等技巧減少時間即可

[Github在此](https://github.com/phillui-37/leetcode-memo/blob/main/1-100/46.hpp)

2023-05-03: Haskell版本完成，沒有甚麼特別，就是不是tail call就是了 [hs github](https://github.com/phillui-37/leetcode-memo/blob/main/1-100/46.hs)