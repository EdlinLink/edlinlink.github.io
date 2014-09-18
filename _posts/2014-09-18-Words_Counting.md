---
layout: post
title:  "Words Counting"
date:   2014-09-18 12:00:00
tags:	tech
myTag:	entertainment

---

# Words Counting

-----------------------------

These days I was doing some boring work, so I'd like to do something fun now.

-----------------------------

### Letter Frequency in Words

At first, I was wondering to do "Letter Frequency in Dictionary". However The biggest problem is how to get a electronic dictionary. After searching the Internet, I gave up but found this post [Dictionary text file](http://stackoverflow.com/questions/4456446/dictionary-text-file). So I use `/usr/share/dict/words` in the computer instead of searching for a dictionary.

Easy code in python:

	dict = {}
	file = open("words", "r")
	all = file.readlines()
	row = len(all)
	total_length = 0

	for i in range(0,row):
		length = len(all[0]) - 1
		total_length +=length
		for j in range(0,length):
			x = all[i][j]

			try:
				dict[x] = dict[x] + 1
			except:
				dict[x] = 0

	keys = dict.keys()

	percent = 0
	for k in keys:
		print k, str (int(dict[k]*10000.0/total_length)/100.0) +"%"
		percent += dict[k]

	print percent
	print total_length
	print percent*1.0 /total_length

The result:
	
	A 1.07%
	C 1.04%
	B 0.57%
	E 0.38%
	D 0.38%
	G 0.42%
	F 0.19%
	I 0.2%
	H 0.46%
	K 0.2%
	J 0.17%
	M 0.77%
	L 0.43%
	O 0.26%
	N 0.27%
	Q 0.03%
	P 0.95%
	S 0.96%
	R 0.27%
	U 0.08%
	T 0.64%
	W 0.13%
	V 0.14%
	Y 0.05%
	X 0.03%
	Z 0.09%
	a 6.18%
	c 7.41%
	b 4.11%
	e 3.32%
	d 4.23%
	g 2.48%
	f 2.71%
	i 3.53%
	h 3.35%
	k 0.73%
	j 0.49%
	m 4.55%
	l 2.21%
	o 3.07%
	n 2.59%
	q 0.45%
	p 9.43%
	s 9.68%
	r 3.81%
	u 6.88%
	t 4.84%
	w 1.53%
	v 1.31%
	y 0.22%
	x 0.12%
	z 0.3%
	234884
	234936
	0.999778663125

The most frequency letter in vocabulary is: **s(9.68%), p(9.43%), c(7.41%), u(6.88%), a(6.18%)**. Please pay attention to this: here we are talking about the letter frequency in vocabulary but not letter frequency in normal use. Because the frequency of each word is different.

### Letter-pair Frequency in Words

Okey. How about the most frequency letter-pair? In the normal use "th" is the most frequency letter-pair (told by my Professor). In all the words they are: **er(2.099%), in(1.664%), ti(1.564%), on(1.474%), te(1.447%)**.

Code here:

	dict = {}
	file = open("words", "r")
	all = file.readlines()
	row = len(all)

	for i in range(0,row):
		length = len(all[i]) - 1

		for j in range(0,length-1):
			x = all[i][j:j+2]
			try:
				dict[x] = dict[x] + 1
			except:
				dict[x] = 0

	keys = dict.keys()

	percent = 0
	for k in keys:
		percent += dict[k]

	L = []

	for k in keys:
		L.append((k, int(dict[k]*100000.0/percent)/1000.0))

	sort_L = sorted(L, cmp=lambda x,y:cmp(x[1],y[1]))

	for i in sort_L:
		print i

### Why the keyboard nowadays like this? 

I was curiosity about why the keyboard nowadays like this? The most frequency letter is not under the forefinger. I found the reason here [(Why the layout of the letters on keyboard like this)](http://zhidao.baidu.com/link?url=5prXhdDQkQEvxc8S_HBosiqmWzerzFNBUXr0c00HHeAyv0DG8m3DGSbJ_FktuRhhKKWbPrEH0sHYbl227VRFQq). It is in order to prevent keys stucking in the typewriters times, which make the keyboard layout toady.
