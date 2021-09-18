> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.cnblogs.com](https://www.cnblogs.com/bbsno1/p/3265349.html)

       有序队列（sorted_queue）其实称作有序表（sorted_list）更好，基于各种考虑，我将 sorted_queue 的底层容器设为 deque，看起来好像折中了 vector 和 list。

       sorted_queue 的插入保证有序，故有移动元素的开销，同样删除操作也会移动元素。但是，sorted_queue 支持随机访问，提供随机迭代器，对有序区间的算法都可用在上面。sorted_queue 的定义还可改进，比如可以增加带参的构造函数，swap 成员方法等。

       sorted_queue 的定义及实现如下：

```
/********************************************************************
	created:	2013/08/16
	created:	16:8:2013   23:56
	file base:	sorted_queue
	file ext:	h
	author:		Justme0 (http://blog.csdn.net/Justme0)
	
	purpose:	sorted_queue 的定义与实现
*********************************************************************/

#ifndef SORTED_QUEUE_H
#define SORTED_QUEUE_H

#include <deque>
#include <algorithm>

template <class T>
class sorted_queue {
public:
	typedef typename std::deque<t>::value_type		value_type;
	typedef typename std::deque<t>::size_type			size_type;
	typedef typename std::deque<t>::reference			reference;
	typedef typename std::deque<t>::const_reference	const_reference;
	typedef typename std::deque<t>::iterator			iterator;
	typedef typename std::deque<t>::const_iterator	const_iterator;

protected:
	std::deque<T> c;	// 底层容器

public:
	iterator begin() {
		return c.begin();
	}

	const_iterator begin() const {
		return c.begin();
	}

	iterator end() {
		return c.end();
	}

	const_iterator end() const {
		return c.end();
	}

	/*
	** 直接存取
	*/
	reference operator[](size_type n) {
		return c[n];
	}

	const_reference operator[](size_type n) const {
		return c[n];
	}

	bool empty() const {
		return c.empty();
	}

	size_type size() const {
		return c.size();
	}

	reference front() {
		return c.front();
	}

	const_reference front() const {
		return c.front();
	}

	reference back() {
		return c.back();
	}

	const_reference back() const {
		return c.back();
	}

	/*
	** 插入元素
	*/
	void push(const value_type & x) {
		c.insert(lower_bound(c.begin(), c.end(), x), x);
	}

	void pop_front() {
		c.pop_front();
	}

	void pop_back() {
		c.pop_back();
	}

	iterator erase(const_iterator position) {
		return c.erase(position);
	}

	iterator erase(const_iterator first, const_iterator last) {
		return c.erase(first, last);
	}

	/*
	** 删除所有值为 x 的元素
	*/
	void remove(const value_type & x) {
		/*
		** 下面这条语句中应该是不得不用 auto，因为不知道返回的是 iterator 还是 const_iterator。
		** 用 equal_range 效率较高。
		*/
		auto interval = equal_range(this->begin(), this->end(), x);
		this->erase(interval.first, interval.second);
	}

	void clear() {
		c.clear();
	}
};

#endif
```

测试程序

```
/********************************************************************
	created:	2013/08/16
	created:	16:8:2013   23:42
	file base:	test
	file ext:	cpp
	author:		Justme0 (http://blog.csdn.net/Justme0)
	
	purpose:	sorted_queue 的测试程序
*********************************************************************/

#include <iostream>
#include "sorted_queue.h"
using namespace std;

template <class T>
void output(const sorted_queue<T> &sq) {
	for (auto ite = sq.begin(); ite != sq.end(); ++ite) {
		cout << *ite << " ";
	}
	cout << endl;
}

int main(int argc, char **argv) {
	sorted_queue<int> sq;

	sq.push(1);
	sq.push(-1);
	sq.push(0);
	sq.push(0);

	output(sq);
	cout << "[3]=" << sq[3] << endl;
	cout << "front=" << sq.front() << endl;
	cout << "back=" << sq.back() << endl;
	cout << (sq.empty() ? "empty" : "not empty") << endl;

	cout << endl;
	cout << "erase 0" << endl;
	sq.remove(0);
	output(sq);

	cout << endl;
	cout << "clear" << endl;
	sq.clear();
	cout << (sq.empty() ? "empty" : "not empty") << endl;

	return 0;
}
```

测试结果

```
-1 0 0 1
[3]=1
front=-1
back=1
not empty

erase 0
-1 1

clear
empty
请按任意键继续. . .
```

参考资料

《STL 源码剖析》

[http://msdn.microsoft.com/en-us/library/ms132319.aspx](http://msdn.microsoft.com/en-us/library/ms132319.aspx)