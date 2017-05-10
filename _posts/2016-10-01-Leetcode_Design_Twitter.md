---
layout: post
title:  "Leetcode - 355. Design Twitter"
date:   2016-10-01 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [355. Design Twitter](https://leetcode.com/problems/design-twitter/)

Design a simplified version of Twitter where users can post tweets, follow/unfollow another user and is able to see the 10 most recent tweets in the user's news feed. Your design should support the following methods:

+ postTweet(userId, tweetId): Compose a new tweet.
+ getNewsFeed(userId): Retrieve the 10 most recent tweet ids in the user's news feed. Each item in the news feed must be posted by users who the user followed or by the user herself. Tweets must be ordered from most recent to least recent.
+ follow(followerId, followeeId): Follower follows a followee.
+ unfollow(followerId, followeeId): Follower unfollows a followee.

Example:

	Twitter twitter = new Twitter();
	
	// User 1 posts a new tweet (id = 5).
	twitter.postTweet(1, 5);
	
	// User 1's news feed should return a list with 1 tweet id -> [5].
	twitter.getNewsFeed(1);
	
	// User 1 follows user 2.
	twitter.follow(1, 2);
	
	// User 2 posts a new tweet (id = 6).
	twitter.postTweet(2, 6);
	
	// User 1's news feed should return a list with 2 tweet ids -> [6, 5].
	// Tweet id 6 should precede tweet id 5 because it is posted after tweet id 5.
	twitter.getNewsFeed(1);
	
	// User 1 unfollows user 2.
	twitter.unfollow(1, 2);
	
	// User 1's news feed should return a list with 1 tweet id -> [5],
	// since user 1 is no longer following user 2.
	twitter.getNewsFeed(1);
	
### 1. Analyse

`Map` can solve everything.

### 2. AC Code

(66 Lines)

	class Twitter {                                                                                   
    public:
        struct Msg{
            int tweetId; 
            unsigned int timestamp;
            Msg( int tid, unsigned int t ) : tweetId(tid), timestamp(t) {};
        };

        /** Initialize your data structure here. */
        Twitter() {
            ts = 0;
        }
        
        /** Compose a new tweet. */
        void postTweet(int userId, int tweetId) {
            account[userId].push_back(Msg(tweetId, ts++));
        }
        
        /** Retrieve the 10 most recent tweet ids in the user's news feed. 
            Each item in the news feed must be posted by users who the user followed or by the user herself. 
            Tweets must be ordered from most recent to least recent. */
        vector<int> getNewsFeed(int userId) {
            vector<Msg> news;
            set<int> myRelation = relation[userId];
            myRelation.insert(userId);
            for( set<int>::iterator it=myRelation.begin(); it!=myRelation.end(); it++ ){
                for( int i=account[*it].size()-1; i>=0; i-- ){
                    if( news.size() < 10 || account[*it][i].timestamp > news.back().timestamp )
                        Push( news, account[*it][i] );
                    else
                        break;
                }   
            }

            vector<int> newsFeed;
            for( int i=0; i<news.size(); i++ )
                newsFeed.push_back( news[i].tweetId );
            return newsFeed;
        }           
        /** Follower follows a followee. If the operation is invalid, it should be a no-op. */
        void follow(int followerId, int followeeId) {
            relation[followerId].insert( followeeId );  
        }   
            
        /** Follower unfollows a followee. If the operation is invalid, it should be a no-op. */
        void unfollow(int followerId, int followeeId) {
            relation[followerId].erase( followeeId );
        }   

        void Push( vector<Msg> & news, const Msg & msg ){
            if( news.size() < 10 )
                news.push_back(msg);
            else if( msg.timestamp > news.back().timestamp )
                news.back() = msg;
            sort( news.begin(), news.end(), cmp );
        }   

        static bool cmp( const Msg & a, const Msg & b ){
            return a.timestamp >= b.timestamp;
        }   
                      
     private:    
        map<int,vector<Msg> > account; 
        map<int,set<int> > relation;
        unsigned int ts;
    };  
	/**
	 * Your Twitter object will be instantiated and called as such:
	 * Twitter obj = new Twitter();
	 * obj.postTweet(userId,tweetId);
	 * vector<int> param_2 = obj.getNewsFeed(userId);
	 * obj.follow(followerId,followeeId);
	 * obj.unfollow(followerId,followeeId);
	 */

Here we use a `ts` variable to keep the order. We should use `time(NULL)` in partical work.
