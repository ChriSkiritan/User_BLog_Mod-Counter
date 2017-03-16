==========  DESCRIPTION OF ISSUES  ==========
Most counter errors occur when editing/deleting etc pending (unapproved or soft-deleted) blogs or replies.
Counter mostly decrements more than expected and just before taking negative values, you get one of the error messages: 
BIGINT UNSIGNED value is out of range in '(`mydatabase`.`phpbb_users`.`blog_count` - 1)' [1690]
BIGINT UNSIGNED value is out of range in '(`mydatabase`.`phpbb_blogs`.`blog_reply_count` - 1)' [1690]
BIGINT UNSIGNED value is out of range in '(`mydatabase`.`phpbb_blogs_categories`.`blog_count` - 1)' [1690]


==========  FIXING COUNTER ISSUES  ==========

(A) FILES TO REPLACE :

/forum/blog/includes/functions_categories.php

/forum/blog/blog/
edit.php, approve.php, delete.php, undelete.php

/forum/blog/reply/
add.php, edit.php, approve.php, delete.php, undelete.php

ATT. i) You will find a second option for /blog/edit.php as well as /reply/edit.php.
If you would like an approved blog or comment after been edited by unauthorized user, not to return to pending state (awaiting re-approval), but remain approved, use the edit_(no_reapprove_ver).php files (rename them to edit.php).
ii) You don't really need to replace functions_categories.php, but if you do, it won't have any side effects. The updated file has just an addition: "blog_count > 0", meaning it would not allow negative value in number of blogs of a category. 


(B) RESET COUNTERS

1) As administrator run in your browser :
your_domain/your_forum/blog.php?page=resync&mode=blog_count
(replace your_domain/your_forum as appropriate).

2) Correction of "Total Entries" number (Sidebar - Blog Stats). This number must reflect the number of blogs publickly viewable. 
One way to calculate this number is: Go to administrator control panel> .mods> blog settings and temporarily disable "user permissions". In permissions> view user-based permissions, ensure guests can view blog entries. Log out and, as guest, go to "Recent Blog Entries" page and make a note of the number of blog entries you read at the lower right corner (pagination line). This is the number you're looking for. If "Total Entries" number (of sidebar) is not equal to it, go to your_domain/phpMyAdmin in your browser, select your_database> phpbb_config. Press "config_name" for alphabetical sorting, find "num_blogs" and edit its value as appropriate.

3) Correction of "Total Comments" number (Sidebar - Blog stats). This number must reflect the number of replies publickly viewable. That is, replies approved, not soft-deleted, belonging to approved, not soft-deleted blogs. Bear in mind that, though unredable by guest, replies of unapproved blog, are included in the list of "Recent Comments" page as seen by a guest.
So, go to the database in your_domain/phpMyAdmin. Select phpbb_blogs, sort by “blog_approved” and, for each row with “0” value for "blog_approved", write down the value for "blog_reply_count". Once you sum blog_reply_count values, get the difference from the number of replies a guest sees at the lower right corner in "Recent Comments" page of your board (with the prerequisities of step 2) and this is it. Then find "num_blog_replies" in phpMyAdmin> your_database> phpbb_config and fill the proper field.

4) Correction of number of blogs of "category list". 
You do this if you have created blog categories. With the prerequisities of step 2, go inside each category, select "Recent Blog Entries" and make a note of number of blog entries you read in lower right corner. Then go to phpMyAdmin> your_database> phpbb_blogs_categories and inside each one correct the value of "blog_count" accordingly.
To get the values of category list refreshed, open a blog of any category for editing and just press submit.

5) Restore temporary changes you made in step 2.
