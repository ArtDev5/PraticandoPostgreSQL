# Find the upward recommendation chain for member ID 27

    Question: Find the upward recommendation chain for member ID 27: that is, the member who recommended them, and the member who recommended that member, and so on. Return member ID, first name, and surname. Order by descending member id.

    Answer: with recursive recommenders(recommender) as (
	        select recommendedby from cd.members where memid = 27
	        union all
	        select mems.recommendedby
	        	from recommenders recs
	        	inner join cd.members mems
	        		on mems.memid = recs.recommender
            )
            select recs.recommender, mems.firstname, mems.surname
            	from recommenders recs
            	inner join cd.members mems
            		on recs.recommender = mems.memid
            order by memid desc 

# Find the downward recommendation chain for member ID 1

    Question: Find the downward recommendation chain for member ID 1: that is, the members they recommended, the members those members recommended, and so on. Return member ID and name, and order by ascending member id.

    Answer: with recursive recommendeds(memid) as (
	        select memid from cd.members where recommendedby = 1
	        union all
	        select mems.memid
	        	from recommendeds recs
	        	inner join cd.members mems
	        		on mems.recommendedby = recs.memid
	        )
	        select recs.memid, mems.firstname, mems.surname
	        from recommendeds recs
	        inner join cd.members mems on recs.memid = mems.memid
	        order by memid

# Produce a CTE that can return the upward recommendation chain for any member

    Question: Produce a CTE that can return the upward recommendation chain for any member. You should be able to select recommender from recommenders where member=x. Demonstrate it by getting the chains for members 12 and 22. Results table should have member and recommender, ordered by member ascending, recommender descending.

    Answer: with recursive recommenders(recommender, member) as (
            select recommendedby, memid from cd.members union all
            select mems.recommendedby, recs.member from
            recommenders recs inner join cd.members mems on recs.recommender = mems.memid)
            
            select recs.member member, recs.recommender, mems.firstname, mems.surname
            from recommenders recs inner join cd.members mems
            on mems.memid = recs.recommender
            where recs.member = 12 or recs.member = 22
            order by member, recommender desc