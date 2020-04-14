# publish-community_id
Ever want to associate Suricata alerts with *all* of the related Zeek logs?  Or spot a suspicious DNS request in the Zeek dns log and want to find out if there were any alerts related to that particular DNS transaction?  The community_id developed by Corelight and the Suricata Project team, can help you do just that.  

This capability currently exists in both tools, as a configurable option in Suricata and as an add-on package for Zeek.  The current Zeek community_id package contains a Zeek script that adds the community_id value to Zeek's conn log, and conn log only.  This means, if you have a community_id from another tool (say... Suricata) and you want to find all of the Zeek logs related to the ID, you first search by the community_id, then grab the Zeek UID for that connection, and perform another search for logs containing the Zeek UID.  Its a clumsy and resource intensive process.  It can be automated.. but it doesn't need to be this way. 

Why not put the community_id everywhere?  That way, one query (or grep) can return all the logs related to a given connection. 

Unfortunately, Zeek doesn't provide a native mechanism for adding a new field to a log stream based on the contents of the underlying connection record.  This missing functionality is detailed in this [https://github.com/corelight/zeek-community-id/issues/3 issue].  


So, in leiue of a better solution, while we ponder modifying Zeek's source to do this better... we dug deep and wrote all of the Zeek script code needed to safely publish the community_id across all of Zeek's logs.  Its not perfect, but better than nothing...  The publish-community_id package can be used instead of the Zeek script contained in the community_id package and anywhere you expect to see a Zeek UID, you'll also see a community_id.  You can now associate all of your favorite Zeek events with related Suricata alerts, and vice versa, without all the grep and re-greping.

So you know this wasn't done all willy nilly, here are some things we tried to accomplish:

* be Zeek 2.6 through 3.1 compatible - mindful of event name and signature changes across Zeek versions
* make minimal calls to hash_conn() - only calculate the community_id once per connection 
* minimize memory footprint - store the ID value once and reuse it as needed for other app-layer logs 
* deal with asymmetry and loss - accommodate split transactions, request with no reply and reply with no request
* modular and fault tolerant - things won't blow up if you disable a protocol analyzer 

Happy hunting! 





