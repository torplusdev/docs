# Fill ipfs repository with popular content

Command to fill ipfs repo with popular content:
    
    ipfs fill

CID - content id in ipfs network (hash of file)
Each node store information abount CID search requests and store in memory count of request (frequent).
Search requests it is DHT request in IPFS network.
Since the data about frequency store only in RAM it will be reset if ipfs will restart.
Ipfs always have some active connection with another network nodes.
Ipfs request information about frequent content from all active connections. 
Sort it by frequency for fill repo with most popular content.
If get the same CID from several nodes we set frequent value like sum of frequents.
Ipfs get all most popular content while repo have free space.

For run fill run command open terminal and run: 

IPFS_PATH=~/Library/TorPlus/IPFS_data/ /Applications/TorPlus.app/Contents/MacOS/ipfs fill

By the command ipfs will download most popular content while storage will bot full. 
Storage limit writen in ipfs config StorageMax by default 10GB
 