---
layout:     post
title:      Assignment1. Percolation
subtitle:   
date:       2019-07-30
author:     cdh
header-img: img/post-bg-course.jpg
catalog: true
tags:
    - Coursera
    - Algorithm
    - Union-Find
    - Assignment
---

## Assignment Introduction

Write a program to estimate the value of the *[percolation](https://coursera.cs.princeton.edu/algs4/assignments/percolation/specification.php)* threshold via Monte Carlo simulation.

### Percolation

Given a composite systems comprised of randomly distributed insulating and metallic materials: what fraction of the materials need to be metallic so that the composite system is an electrical conductor? Given a porous landscape with water on the surface (or oil below), under what conditions will the water be able to drain through to the bottom (or the oil to gush through to the surface)? Scientists have defined an abstract process known as percolation to model such situations.

### The model

We model a percolation system using an n-by-n grid of sites. Each site is either open or blocked. A **full site** is an open site that can be connected to an open site in the top row via a chain of neighboring (left, right, up, down) open sites. We say the system **percolates** if there is a full site in the bottom row. In other words, a system percolates if we fill all open sites connected to the top row and that process fills some open site on the bottom row. (For the insulating/metallic materials example, the open sites correspond to metallic materials, so that a system that percolates has a metallic path from top to bottom, with full sites conducting. For the porous substance example, the open sites correspond to empty space through which water might flow, so that a system that percolates lets water fill open sites, flowing from top to bottom.)

![](https://github.com/cdhcs1516/cdhcs1516.github.io/raw/master/img/percolation-ex1.png)
![](https://github.com/cdhcs1516/cdhcs1516.github.io/raw/master/img/percolation-ex2.png)

### The problem 

In a famous scientific problem, researchers are interested in the following question: if sites are independently set to be open with probability $p$ (and therefore blocked with probability $1 − p$), what is the probability that the system percolates? When $p$ equals 0, the system does not percolate; when $p$ equals 1, the system percolates. The plots below show the site vacancy probability p versus the percolation probability for 20-by-20 random grid (left) and 100-by-100 random grid (right).

When $n$ is sufficiently large, there is a threshold value p* such that when $p < p^*$ a random n-by-n grid almost never percolates, and when $p > p^*$, a random n-by-n grid almost always percolates. No mathematical solution for determining the percolation threshold $p^*$ has yet been derived. **Your task is to write a computer program to estimate $p^*$.**

### Monte Carlo simulation

To estimate the percolation threshold, consider the following computational experiment:
1. Initialize all sites to be blocked.
2. Repeat the following until the system percolates:
    - Choose a site uniformly at random among all blocked sites.
    - Open the site.
    - The fraction of sites that are opened when the system percolates provides an estimate of the percolation threshold.

## My Solution

### Idea and thought
This assignment is based on the union-find set problem. As the `weightedQuickUnionUF.java` is already provided, it is easy to solve this problem. **The key problem is how to check if the system percolates.** Obviously, if any site on the bottom row is a full site which means the site connects with the top-row site, the system percolates. However, check every `(top-row site, bottom-row site)` pair is very time-consuming. Thus, motivated by the course, I connects all the open sites on the top row to a virtual site which is not really existed. Similarly, all the open sites on the bottom row are connected a virtual site, too. So just check if these two virtual sites are connected can determine whether the system percolates. 

This assignment requires the students to accomplish two classes `Percolation` and `PercolationStats`. The former class tries to model a continuously updated system and provides some API for operating and checking this system. The latter class relies on the *Monte Carlo* theory and takes several simulations for the percolation system. In every cases, record the ratio of open sites $p^*$ when system percolates. Then use `StdRandom.class` and `StdStats.class` to stastically analyze the simulation results, like computing the mean value, the standard deviation and the 95% confidence interval.

### Implementation

The implementations of these two classes are shown as follows.

`Percolation.java`
``` 
import edu.princeton.cs.algs4.WeightedQuickUnionUF;
import java.lang.IllegalArgumentException;

public class Percolation {
	// Private members
	private WeightedQuickUnionUF set;
	private int[] statOfSites; // 0 is blocked, 1 is opened.
	private int numOfSites;
	private int numOfOpen;
	private int gridSize;
	
    // creates n-by-n grid, with all sites initially blocked
    public Percolation(int n) {
    	if (n <= 0) {
    		throw new IllegalArgumentException("Grid size n should be greater than 0.");
    	}
    	
    	gridSize = n;
    	set = new WeightedQuickUnionUF(gridSize * gridSize + 2);
    	statOfSites = new int[gridSize * gridSize + 2];
    	for (int i = 0; i < statOfSites.length; i++) {
    		if (i == 0 || i == statOfSites.length - 1) {
    			statOfSites[i] = 1;
    		}
    		else {
    			statOfSites[i] = 0;
    		}
    	}
    	numOfSites = gridSize * gridSize;
    	numOfOpen = 0;
    }

    // opens the site (row, col) if it is not open already
    public void open(int row, int col) {
    	if (row < 1 || row > gridSize || col < 1 || col > gridSize) {
    		throw new IllegalArgumentException("Indices should be in the range of 1 and " + gridSize + ".");
    	}
    	
    	int index = (row - 1) * gridSize + col;
    	if (statOfSites[index] == 0){
			statOfSites[index] = 1; // modify the status of this site		
			if (row == 1) {
				set.union(0, index);
			}
			if (row == gridSize) {
				set.union(index, numOfSites + 1);
			} // union the site with top or bottom if it is on the first or last row
			
			// up
			if (row > 1 && statOfSites[index - gridSize] == 1) {
				set.union(index, index - gridSize);
			}
			// down
			if (row < gridSize && statOfSites[index + gridSize] == 1) {
				set.union(index, index + gridSize);
			}
			// left
			if (col > 1 && statOfSites[index - 1] == 1) {
				set.union(index, index - 1);
			}
			//right
			if (col < gridSize && statOfSites[index + 1] == 1) {
				set.union(index, index + 1);
			}
			numOfOpen ++;
    	}
    }

    // is the site (row, col) open?
    public boolean isOpen(int row, int col) {
    	if (row < 1 || row > gridSize || col < 1 || col > gridSize) {
    		throw new IllegalArgumentException("Indices should be in the range of 1 and " + gridSize + ".");
    	}
    	
    	return (statOfSites[(row - 1) * gridSize + col] == 1);
    }

    // is the site (row, col) full?
    public boolean isFull(int row, int col) {
    	if (row < 1 || row > gridSize || col < 1 || col > gridSize) {
    		throw new IllegalArgumentException("Indices should be in the range of 1 and " + gridSize + ".");
    	}
    	
    	if (isOpen(row, col)) {
    		return set.connected(0, (row - 1) * gridSize + col);
    	}
    	else {
    		return false;
    	}
    }

    // returns the number of open sites
    public int numberOfOpenSites() {
    	return numOfOpen;
    }

    // does the system percolate?
    public boolean percolates() {
    	return set.connected(0, gridSize * gridSize + 1);
    }

    // test client (optional)
    public static void main(String[] args) {
//    	Percolation tcase = new Percolation(20);
//    	System.out.println("Status of this grid: \n"
//    			+ "Number of sites: " + tcase.numOfSites + "\n"
//    			+ "Number of open sites: " + tcase.numberOfOpenSites() + "\n"
//    			+ "Is Percolates? " + (tcase.percolates()? "Yes":"No"));
//    	int r = 1;
//    	while(r <= 20) {
//    		tcase.open(r, 3);
//    		System.out.println("Is percolates? " + (tcase.percolates()? "Yes":"No"));
//    		r ++;
//    	}
    	
    	for (int i = 0, a = 0; i < 10; a = i++) {
    		System.out.println(a);
    	}
    	for (int i = 0, a = 0; i < 10; a = ++i) {
    		System.out.println(a);
    	}
    }
}
```

`PercolationStats.java`
``` 
import edu.princeton.cs.algs4.StdRandom;
import edu.princeton.cs.algs4.StdStats;
import java.lang.IllegalArgumentException;

public class PercolationStats {
	private Percolation grid;
	private double[] prob;
	private double pmean;
	private double pstd;
	
    // perform independent trials on an n-by-n grid
    public PercolationStats(int n, int trials) {
    	if (n <= 0 || trials <= 0) {
    		throw new IllegalArgumentException("Grid size and trial number should both be greater than 0.");
    	}
    	prob = new double[trials];
    	
    	for (int i = 0; i < trials; i++) {
        	grid = new Percolation(n);
    		while (!grid.percolates()) { // randomly open site until the grid percolates
	    		int r = StdRandom.uniform(n) + 1;
	    		int c = StdRandom.uniform(n) + 1;
	    		
	    		if (!grid.isOpen(r, c)) {
	    			grid.open(r, c);
	    		}
	    	}
    		prob[i] = 1.0 * grid.numberOfOpenSites() / (n * n);
    	}
    	
    }

    // sample mean of percolation threshold
    public double mean() {
    	pmean = StdStats.mean(prob);
    	return pmean;
    }

    // sample standard deviation of percolation threshold
    public double stddev() {
    	pstd = StdStats.stddev(prob);
    	return pstd;
    }

    // low endpoint of 95% confidence interval
    public double confidenceLo() {
    	return pmean - 1.96 * pstd / Math.sqrt((double) prob.length);
    }

    // high endpoint of 95% confidence interval
    public double confidenceHi() {
    	return pmean + 1.96 * pstd / Math.sqrt((double) prob.length);
    }

   // test client (see below)
    public static void main(String[] args) {
    	PercolationStats tcase = new PercolationStats(Integer.parseInt(args[0]), Integer.parseInt(args[1]));
//    	PercolationStats tcase = new PercolationStats(2, 10000);
    	System.out.println("mean\t\t\t= " + tcase.mean());
    	System.out.println("stddev\t\t\t= " + tcase.stddev());
    	System.out.println("95% confidence interval\t= [" + tcase.confidenceLo() + ", " + tcase.confidenceHi() + "]");
   }

}
```

### Judge result

By submitting abvementioned codes, I get a score of 88. The implementations still have one bug which does not affect the estimation of threshold $p^*$. That is the **backwash** which is caused by the bottom virtual site. This bug happens after the system percolates and affects the precision of the method `isFull()`. I temporarily have no idea to solve this bug. Maybe just delete this bottom virtual site and check the connection status of every bottom-row site with the top virtual site? But it seems not a effecient approach.

![backwash](https://github.com/cdhcs1516/cdhcs1516.github.io/raw/master/img/percolation-backwash.png)

