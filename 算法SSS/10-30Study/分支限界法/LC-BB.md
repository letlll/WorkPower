# 使用 Least Cost Branch and Bound 的 0/1 背包

给定 **N** 个重量为 **W[0..n-1]** 的项目，值为 **V[0..n-1]** 和一个容量为 C 的背包，选择项目，以便：  
 

1. 装进背包的砝码之和小于或等于 C。
2. 背包中物品的值之和在所有可能的组合中是最大的。

例子：   
 
>[!example] 示例 
> 输入：N = 4， C = 15， V[]= {10， 10， 12， 18}， W[]= {2， 4， 6， 9}  
> 输出：  
> 带入背包的物品为  
> 1 1 0 1  
> 最大利润为 38  
> 说明：  
> 输出中的 1 表示该物品包含在背包中，而 0 表示该物品被排除在外。  
> 由于允许的最大可能成本为 15，因此选择项目的方法为：  
> （1 1 0 1） -> 成本 = 2 + 4 + 9 = 15，利润 = 10 + 10 + 18 = 38。  
> （0 0 1 1） -> 成本 = 6 + 9 = 15，利润 = 12 + 18 = 30  
> （1 1 1 0） -> 成本 = 2 + 4 + 6 = 12，利润 = 32  
> 因此，在 15 的成本内可能的最大利润为 38。  

>[!example] 示例 
> 输入： N = 4， C = 21， V[]= {18， 20， 14， 18}， W[]= {6， 3， 5， 9}  
> 输出：  
> 背包里的物品是  
> 1 1 0 1  
> 最大利润为 56  
> 解释：  
> 成本 = 6 + 3 + 9 = 18  
> 利润 = 18 + 20 + 18 = 56  
>  

方法：  
在本文中，讨论了使用 Least cost（LC） 实现 [0/1 背包问题的](https://www.geeksforgeeks.org/0-1-knapsack-problem-dp-10/) Branch and Bound 方法。  
[分支限界法](https://www.geeksforgeeks.org/branch-and-bound-algorithm/)可以使用 [FIFO、](https://www.geeksforgeeks.org/fifo-first-in-first-out-approach-in-programming/)[LIFO](https://www.geeksforgeeks.org/lifo-last-in-first-out-approach-in-programming/) 和 LC 策略来解决。**最低成本 （LC）** 被认为是最智能的，因为它根据启发式成本函数选择下一个节点。它会选择成本最低的那个。  
由于 0/1 背负式是关于总价值最大化的，我们不能直接使用 LC 分支限界法来解决这个问题。相反，我们通过对**给定值取负值**将其转换为最小化问题。  
请按照以下步骤解决问题：  
 

1. 根据项目的值/重量 （V/W） 比率对项目进行排序。
2. 将虚拟节点插入[优先级队列](https://www.geeksforgeeks.org/priority-queue-set-1-introduction/)。
3. 重复以下步骤，直到优先级队列为空：
    - 从优先级队列中提取 peek 元素并将其分配给当前节点。
    - 如果当前节点的上限小于 minLB，即探索的所有节点的最小下限，则没有探索点。所以，继续下一个元素。不考虑上限大于 minLB 的节点的原因是，上限存储了可能达到的最佳值。如果 best 值本身不是 minLB最优的，那么探索该路径是没有用的。  
         
    - 更新 path 数组。
    - 如果当前节点的级别为 N，则检查当前节点的下限是否小于 finalLB，即到达 final 级别的所有路径的最小下限。如果为 true，则更新 finalPath 和 finalLB。否则，请继续执行下一个元素。
    - 计算当前节点的右子节点的下限和上限。
    - 如果当前项可以插入背包中，则计算当前节点的左子项的下限和上限。
    - 更新 minLB 并插入子项（如果其上限小于 minLB）。

> 插图：  
> N = 4， C = 15， V[]= {10 10 12 18}， W[]= {2 4 6 9}  
>  
> ![](assets/Pasted%20image%2020241030105442.png)
>
> i 处的左分支和右分支 th level 存储获取的最大值，包括和不包括 ith 元素。  
> 下图显示了每个步骤后优先级队列的状态：  
>  ![](assets/Pasted%20image%2020241030105516.png)
> 
以下是上述方法的实现：

```java
// Java Program to implement
// 0/1 knapsack using LC
// Branch and Bound

import java.util.*;
class Item {

	// Stores the weight
	// of items
	float weight;

	// Stores the values
	// of items
	int value;

	// Stores the index
	// of items
	int idx;
	public Item() {}
	public Item(int value, float weight,
				int idx)
	{
		this.value = value;
		this.weight = weight;
		this.idx = idx;
	}
}

class Node {
	// Upper Bound: Best case
	// (Fractional Knapsack)
	float ub;

	// Lower Bound: Worst case
	// (0/1)
	float lb;

	// Level of the node in
	// the decision tree
	int level;

	// Stores if the current
	// item is selected or not
	boolean flag;

	// Total Value: Stores the
	// sum of the values of the
	// items included
	float tv;

	// Total Weight: Stores the sum of
	// the weights of included items
	float tw;
	public Node() {}
	public Node(Node cpy)
	{
		this.tv = cpy.tv;
		this.tw = cpy.tw;
		this.ub = cpy.ub;
		this.lb = cpy.lb;
		this.level = cpy.level;
		this.flag = cpy.flag;
	}
}

// Comparator to sort based on lower bound
class sortByC implements Comparator<Node> {
	public int compare(Node a, Node b)
	{
		boolean temp = a.lb > b.lb;
		return temp ? 1 : -1;
	}
}

class sortByRatio implements Comparator<Item> {
	public int compare(Item a, Item b)
	{
		boolean temp = (float)a.value
						/ a.weight
					> (float)b.value
							/ b.weight;
		return temp ? -1 : 1;
	}
}

class knapsack {

	private static int size;
	private static float capacity;

	// Function to calculate upper bound
	// (includes fractional part of the items)
	static float upperBound(float tv, float tw,
							int idx, Item arr[])
	{
		float value = tv;
		float weight = tw;
		for (int i = idx; i < size; i++) {
			if (weight + arr[i].weight
				<= capacity) {
				weight += arr[i].weight;
				value -= arr[i].value;
			}
			else {
				value -= (float)(capacity
								- weight)
						/ arr[i].weight
						* arr[i].value;
				break;
			}
		}
		return value;
	}

	// Calculate lower bound (doesn't
	// include fractional part of items)
	static float lowerBound(float tv, float tw,
							int idx, Item arr[])
	{
		float value = tv;
		float weight = tw;
		for (int i = idx; i < size; i++) {
			if (weight + arr[i].weight
				<= capacity) {
				weight += arr[i].weight;
				value -= arr[i].value;
			}
			else {
				break;
			}
		}
		return value;
	}

	static void assign(Node a, float ub, float lb,
					int level, boolean flag,
					float tv, float tw)
	{
		a.ub = ub;
		a.lb = lb;
		a.level = level;
		a.flag = flag;
		a.tv = tv;
		a.tw = tw;
	}

	public static void solve(Item arr[])
	{
		// Sort the items based on the
		// profit/weight ratio
		Arrays.sort(arr, new sortByRatio());

		Node current, left, right;
		current = new Node();
		left = new Node();
		right = new Node();

		// min_lb -> Minimum lower bound
		// of all the nodes explored

		// final_lb -> Minimum lower bound
		// of all the paths that reached
		// the final level
		float minLB = 0, finalLB
						= Integer.MAX_VALUE;
		current.tv = current.tw = current.ub
			= current.lb = 0;
		current.level = 0;
		current.flag = false;

		// Priority queue to store elements
		// based on lower bounds
		PriorityQueue<Node> pq
			= new PriorityQueue<Node>(
				new sortByC());

		// Insert a dummy node
		pq.add(current);

		// curr_path -> Boolean array to store
		// at every index if the element is
		// included or not

		// final_path -> Boolean array to store
		// the result of selection array when
		// it reached the last level
		boolean currPath[] = new boolean[size];
		boolean finalPath[] = new boolean[size];

		while (!pq.isEmpty()) {
			current = pq.poll();
			if (current.ub > minLB
				|| current.ub >= finalLB) {
				// if the current node's best case
				// value is not optimal than minLB,
				// then there is no reason to
				// explore that node. Including
				// finalLB eliminates all those
				// paths whose best values is equal
				// to the finalLB
				continue;
			}

			if (current.level != 0)
				currPath[current.level - 1]
					= current.flag;

			if (current.level == size) {
				if (current.lb < finalLB) {
					// Reached last level
					for (int i = 0; i < size; i++)
						finalPath[arr[i].idx]
							= currPath[i];
					finalLB = current.lb;
				}
				continue;
			}

			int level = current.level;

			// right node -> Excludes current item
			// Hence, cp, cw will obtain the value
			// of that of parent
			assign(right, upperBound(current.tv,
									current.tw,
									level + 1, arr),
				lowerBound(current.tv, current.tw,
							level + 1, arr),
				level + 1, false,
				current.tv, current.tw);

			if (current.tw + arr[current.level].weight
				<= capacity) {

				// left node -> includes current item
				// c and lb should be calculated
				// including the current item.
				left.ub = upperBound(
					current.tv
						- arr[level].value,
					current.tw
						+ arr[level].weight,
					level + 1, arr);
				left.lb = lowerBound(
					current.tv
						- arr[level].value,
					current.tw
						+ arr[level].weight,
					level + 1,
					arr);
				assign(left, left.ub, left.lb,
					level + 1, true,
					current.tv - arr[level].value,
					current.tw
						+ arr[level].weight);
			}

			// If the left node cannot
			// be inserted
			else {

				// Stop the left node from
				// getting added to the
				// priority queue
				left.ub = left.lb = 1;
			}

			// Update minLB
			minLB = Math.min(minLB, left.lb);
			minLB = Math.min(minLB, right.lb);

			if (minLB >= left.ub)
				pq.add(new Node(left));
			if (minLB >= right.ub)
				pq.add(new Node(right));
		}
		System.out.println("Items taken"
						+ "into the knapsack are");
		for (int i = 0; i < size; i++) {
			if (finalPath[i])
				System.out.print("1 ");
			else
				System.out.print("0 ");
		}
		System.out.println("\nMaximum profit"
						+ " is " + (-finalLB));
	}

	// Driver code
	public static void main(String args[])
	{
		size = 4;
		capacity = 15;

		Item arr[] = new Item[size];
		arr[0] = new Item(10, 2, 0);
		arr[1] = new Item(10, 4, 1);
		arr[2] = new Item(12, 6, 2);
		arr[3] = new Item(18, 9, 3);

		solve(arr);
	}
}

```