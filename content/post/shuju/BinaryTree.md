---
title: "用C++实现一个二叉树"
date: 2020-04-25T06:20:47+08:00
draft: true
---

### 什么是二叉树

在计算机科学中，二叉树是每个结点最多有两个子树的树结构。通常子树被称作“左子树”（left subtree）和“右子树”（right subtree）。

### 二叉树的主要特点

查找较为方便，如果是左子树比节点小，右子树比节点大的这种方式，在查找的过程中其实就实现了一个”部分“的二分查找。这种二叉树只要不是偏二叉树的话，查找的效率会比线性表快很多。

二叉树不是线性表，因为它并不是线性结构，而是树形的。二叉树不能一次就遍历完整棵树，而是要通过递归的方式。

优点：搜索和排序效率极高
缺点：插入和删除略微麻烦，且实现起来比链表更难。

[源码](http://localhost:1313/post/binarytree/)

### 二叉树的具体实现

#### 1.类定义

```cpp
class Node
{
public:
	int data;
	Node * left; //左右子树
	Node * right;
public:
	Node();
};

class BTree
{
public:
	Node * root;
};
```

二叉树的类定义和其他数据结构其实没有太大的区别，节点类包括元素值，左右子树的指针，而二叉树本身就只有一个根节点了。

#### 2.构造函数

```cpp
BTree::BTree()
{
	root = nullptr;
}
```

构造函数我个人喜欢简单的实现，这里只有最基本的初始化根节点的操作。

#### 3.析构函数

```cpp
BTree::~BTree()
{
	Node * p = root;
	destory(p);
}
```

析构函数其实是调用了一个销毁二叉树的方法，这个函数之后再说。

#### 4.创建二叉树

```cpp
//创建二叉树 递归
void BTree::create(Node * &p,Node * &temp)
{
    //保证二叉树满足 左子树小于根节点，右子树大于根节点
    //p是新的节点，temp是从root开始遍历的节点
	if(temp == nullptr)
	{
		temp = p;
	}
	else
	{
		//根节点有值了，判断大小
		if(temp->data == p->data )
		{
			cout<<"the same value is not allowed"<<endl;
		}
        else if(p->data < temp->data) //新节点比根节点小，就去左边递归
        {
            create(p,temp->left);
		}
        else //p->data > root->data //反之去右边递归
		{
            create(p,temp->right);
		}

	}

}
```

创建二叉树的过程稍微简单一点，我选择先输入根节点的值，创建二叉树，之后再调用这个函数去通过递归的方式创建各个子树。

外部代码如下：
```cpp
    cout<<"enter the root's value"<<endl;
    tree.root = new Node;
    cin>>tree.root->data;
    while(1)
    {
        cout<<"enter the value or -999 to exit"<<endl;
        cin>>value;
        if(value == -999)
        {
            break;
        }

        Node * p =new Node;
        //

        p->data = value;
        p->left = nullptr;
        p->right = nullptr;
        tree.create(p,tree.root);

    }
    cout<<"the binary tree has been created"<<endl;
```

这样一来，就不需要判断二叉树是否为空，当前是否为根节点等等问题了，函数内部只需要关心创建的细节。

在创建的时候，我选择让二叉树的左子树全部小于根节点，右子树全部大于根节点，这就实现了一个**二叉搜索树**。同时不支持重复的节点，想看看能否通过代码去判断出这一问题。

#### 5.遍历

```cpp
//中序遍历
void BTree::showroot(Node * p)
{
	if(p!=nullptr)
	{
		showroot(p->left);
		cout<<p->data<<endl;
		showroot(p->right);
	}
}


//先序遍历
void BTree::showhead(Node *p)
{
	if(p!=nullptr)
	{
		cout<<p->data<<endl;
		showhead(p->left);
		showhead(p->right);

	}
}


//后序遍历
void BTree::showtail(Node *p)
{
	if(p!=nullptr)
	{

		showhead(p->left);
		showhead(p->right);
		cout<<p->data<<endl;
	}
}
```

二叉树的遍历是我见过最简单的数据结构代码，没有之一。通过递归调用，考虑清楚是先递归还是先输出根节点，就可以实现了。

#### 6.插入

```cpp
void BTree::insert(int value,Node * &p,Node * last)
{
	if(p == nullptr&&p==root) //如果根节点为空，表示这是一个空链表，需要new根节点了
	{
		root = new Node;
		root->data = value;
		return;
	}
	if(p!=nullptr)  //当前位置无法提供插入
	{
        cout<<"当前的值为"<<p->data<<endl;
        if(p->data > value) //当前节点比要插入的值大 往左边寻找空节点
		{
            insert(value,p->left,p);
		}
        else if(p->data < value) //反之去右边寻找
		{
            insert(value,p->right,p);
		}
		else //重复的元素暂不支持
		{
			cout<<"the same value is not be allowed"<<endl;
			return ;
		}
	}
	else if(p==nullptr)
	{
		p = new Node;
		p->data = value;
		if(p->data>last->data)
		{
			last->right = p;//左边比右边小
		}
		else
		{
			last ->left = p;
		}
		return ;

	}
}
```

插入也是用递归查找的方式，当目前的节点有值的时候，判断比这个值大还是小，就往左右子树进行遍历。

#### 7.查询

```cpp
Node * BTree::search(int value, Node * &p)
{
	if(p == nullptr)
	{
		cout<<"value not found"<<endl;
		return nullptr;
	}
	else if(p->data == value)
	{
		cout<<"value has been found "<<endl;
		cout<< p<<endl;
		return p;
	}
	else if(p->data<value)
	{
        return search(value,p->right);
	}
	else
	{
        return search(value,p->left);
	}
}
```

典型的二分查找，思路和插入是一样的。

#### 8.插入根节点
```cpp
void BTree::insertRoot(Node *&p, Node *&temp)
{

	//p是新的节点，temp是之前的head
	if(temp == nullptr)
	{
		root = p; //空树
	}
	else
	{
        if(search(p->data,temp))
        {
            cout<<"the same value is not allowed"<<endl;
            return;
        }
		if(p->data == temp->data)
		{
			cout<<"the same value is not allowed"<<endl;
		}
		else if(p->data>temp->data)//新的根节点比之前的大
		{
			p->left = temp;
			root = p;
		}
		else //新的根节点比之前的小，那么右子树就是原先的根节点了
		{
			p->right = temp;
			root = p;
		}
	}

}
```

一个没有太大用的功能。

#### 9.求长度（节点的个数）

```cpp
int BTree::getlength(Node *p)
{
	if(p == nullptr)
	{
		return 0;
	}
	else
	{
		return getlength(p->left)+1+getlength(p->right);
	}
}
```

通过递归的方式，每当这个节点有值，二叉树的节点个数就+1，并且递归到下一个节点

#### 10.求深度

```cpp
int BTree::getdepth(Node *p,int depth)
{
	if(p==nullptr)
	{
		return depth;
	}
	return max(getdepth(p->left,depth+1),getdepth(p->right,depth+1));
}
```
代码和求长度比较类似，但核心在于求出左右子树深度的最大值，而不是累加在一起。

#### 11.删除某个节点

```cpp
Node * BTree::deleteNode(int value, Node * &p)
{
	if(p == nullptr)
	{
		cout<<"data not found"<<endl;
		return p;//空节点不能删除
	}
	else if (value < p->data)
	{
        p->left = deleteNode(value,p->left); //删除后的值改对应链接
	}
	else if(value > p->data)
	{
        p->right = deleteNode(value,p->right);
	}
	else //查找到元素 value == p->data
	{
		cout<<"found"<<endl;
		if(p->left == nullptr && p->right == nullptr) //删除的节点是叶子
		{
			delete p;
			p = nullptr;
		}
		else if (root->data == value) //当为根节点时候
		{
            //也可以不让删除
			Node * tempR = root->right; //为了遍历出右子树的最大值
			Node * tempL = root->left;
			Node * temp = root->right;  //记录当前的右节点的指针
			if(tempR == nullptr && tempL != nullptr )
			{
				root  = tempL;
			}
			else if (tempR != nullptr && tempL == nullptr)
			{
				root  = temp;
			}
			else   //都有数据
			{
				while(tempR->left != nullptr)  //找到右子树最大的值
				{
					tempR = tempR->left;
				}
				tempR->left = tempL; //让右子树最大值的的左节点拼接最初的左节点
				root = temp;
			}
			return root;
		}
		else if(p->left == nullptr && p->right != nullptr) //当节点左空 右有 时
		{
			  Node * temp = p->right; //把右边节点记录下来
			  delete p;
			  return  temp; //返回上个节点的指向
//            Node * temp = p;
//            p = p->right;
//            delete temp;
//            temp = nullptr;
//            return  temp;
		}
		else if(p->right == nullptr && p->left != nullptr) //当节点右空 左有 时
		{
			 Node * temp = p->left;
			 delete p;
			 return  temp; //返回上个节点的指向
//            Node * temp = p;
//            p = p->left;
//            delete temp;
//            temp = nullptr;
//            return  temp;
		}
		else    //当两边都有节点时
		{

			Node * tempR = p->right; //为了遍历出右子树的最大值
			Node * tempL = p->left;
			Node * temp = p->right;  //记录当前的右节点的指针
			delete p;
			while(tempR->left != nullptr)  //找到右子树最大的值
			{
				tempR = tempR->left;
			}
			tempR->left = tempL; //让右子树最大值的的右节点拼接最初的左节点
			return temp;

//            while(p->left != nullptr) //找到最小的节点
//            {
//                p = p->left;
//            }
//            Node * temp = p;
//            p->data = temp->data;
//            p->right = deleteNode(temp->data,p->right);

		}

	}
	return p;
}
```

分四种情况：

1.该节点是叶子，直接置为空指针，释放内存空间即可。

2.该节点有左子树或右子树，删除该节点，下一个节点连接上去。

3.该节点有左右子树，最麻烦的一种情况，需要求出子树中的最大值，使他成为目前的节点。

4.找不到该元素。

由此可看出，删除二叉树节点的第一步，其实是遍历查找这个节点。

#### 12.销毁该二叉树

其实就是博客前面提到的析构函数。

```cpp
void BTree::destory(Node * &p)
{
	if(p != nullptr)
	{
		destory(p->left);
		destory(p->right);
		delete p;
		p = nullptr;
	}
}
```

相比之前的代码，算是比较简单的了。


### 总结

二叉树的实现相比链表，还是要复杂很多。文中实现的是一个二叉搜索树，比普通的二叉树稍微多了一点细节，但实用程度却大了很多。

通过学习二叉树，对递归的理解想必也可以得到加深了。




                