# raft 阅读随笔
raft 协议三个主要的部分  

* Leader election 
* Log replication
* Safty

raft协议中的三种角色

* Leader
* Follower
* Candidate

**Term**：可以理解为任期， 一个Leader有一个任期, Term不一定是连续的，但是一定是单调递增的，因为可能在某次Leader election中没有选出Leader， 导致Term+1,然后+1重新选择 ?    这块理解应该没错吧

每个server都要存currentTerm， 请求中都会带上这个值，来判断当前任期或者logEntry是否过期


Raft 协议中的三种RPC:

* RequestVote：
* AppendEntries: (heartBeats: AppendEntries with no log entries)
* TransferSnapshot

## Leader Election

Election timeout: 选举超时时间，通常每个server都不一样，是某个区间的中的随机值, 防止(split vote: 选举不出leader)

1. 对于一个服务器，初始状态是follower
2. follower->candidate: ElectionTimeout内没有收到Leader的heartBeat OR candidate的`RequestVote`:
        currentTerm+1, 
        vote for himself and 
        issue RequestVote RPC
        
        * Win Election: n/2+1 votes, 每个server 每个term只能为一个candidate投票 first-come-first-serve
        * No Winner: timeout, new Election, addition measures.
        * Other Server wins: other server term>= 本server term, candidate-> follower, else reject rpc call. 



## Log replication
Leader: 

1. 接受请求client请求，如果client发给不是leader而是follower， 则follower会reject， 然后把leader的ip带给client，client重新发起请求
2. Append the request to log entry.  logentry: termNumber, and index
3. send AppendEntries RPC call
4. success, commit. previous entry are also committed
5. return the commit result to the client

leader 保存Highest index committed, 这个index 会在AppendEntries RPC call， 
follower根据这个index， commit本地的logEntry

In Raft, The leader handles inconsistencies by forcing the followers log to duplicate its own:
  首先找到match points. AppendEntries  会有consistency check， 会找到Term+index的match点。 Leader会维护每个follower的nextIndex
  
  
  
## Safty


