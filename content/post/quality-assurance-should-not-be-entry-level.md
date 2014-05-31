{
  "title": "Quality Assurance should not be entry-level ",
  "description": "Quality Assurance should not be entry-level ",
  "date": "2010-12-02",
  "url": "quality-assurance-should-not-be-entry-level/",
  "type": "post",
  "tags": [
    "qa"
  ]
}
When it comes to software development, I am passionate about quality.  I admit that I don't always put my blog posts through a rigorous spelling, grammar, and formatting check, but I'll claim to be the ever-popular 'plumber with leaky pipes' regarding this blog.  I genuinely like finding bugs.  More explicitly, I like finding out when a bug occurs, and why.  And as both a Quality Assurance snob, and a software developer, I don't care, nor do I have time for bugs like:

--
"the Duke character sprite artifacts on the credits screen - artifacts should not be present."  
--

Rather, I care that:

--
"The Duke" character sprite remains on screen during credits display after beating the game under certain circumstances.

1\.  Launch "The Duke" on platform y.
2\.  From Main screen, Select "New Game".
3\.  Choose options configuration 4, controls configuration B, and sound Off.  Select "MFing Go Time!" to proceed to gameplay.
4\.  Without dying and with dev cheats off, be sure to pickup the double-rocket-launcher in level 2 (By blowing up the 3 radioactive boxes with a single bomb placed in the middle), and do not use it or lose it.  Proceed to level 6 and use the warp to get to level 10 (last level).
5\.  Get to "The End Boss" and immediately after intro music stops (as soon as you can start firing, rapid fire all 25 of your rockets using the double rocket launcher).
6\.  Once all rockets hit "The End Boss", he dies, and the finale music interrupts the main "The End Boss" guitar riff.  At this point the screen flashes white then black - instead of the usually fade out to black.  All in-game images disappear except "The Duke" with his double-rocket-launcher (who remains in his last position, and credits roll.

Expected Result:  "The Duke" must disappear consistently during the end game credits sequence, or always remain on screen during the credits.

Actual Result:  The double-rocket-launcher is the only weapon/technique that allows "The Duke" to kill "The End Boss" before his main guitar riff ends.  When this happens, something different occurs that breaks the fade-out effect and causes "The Duke" image to remain on screen.

NOTE:  I was able to replicate 3 times as described above.  I attempted to kill "The End Boss" with all other weapons and a few of the stronger combinations, but could not get more than half damage before the end of the main guitar riff sound.  This may occur when the user dies, or when the user plays through the game without using the warp, but including these steps allows for quickest replication.
--

No entry level quality assurance analyst writes bugs effectively.  It is not an entry level task.  You know what an entry level position holder can write?  

<pre>if( music[GUITAR_RIFF].isPlaying() ){
    music[GUITAR_RIFF].stopPlaying();
}</pre>

With that premise, I propose the following paradigm shift:  Junior developers are the entry level position.  As junior developers mature, most senior devs and team leads get a sense for how good they are going to be at developing on their own.  Some have great attention to detail, some have great API recollection, some have both.  These are certainly not the only traits, nor do they represent an adequate spectrum, but you get the idea.  During the initial review and training exercises involving a junior developer, senior team members can tell if they are cut out for development.  Making this determination is no easy task, as there is a great deal of overlap in the required skill sets, nonetheless, I think it is quite possible.  

Beyond the fact that entry level position holders are more likely to be competent at writing efficient code than effective bugs, there is definite manpower boost.  Taking my initial example of "the Duke character sprite artifacts on the credits screen - artifacts should not be present.".  This bug inevitably goes to a junior developer.  The Junior developer Launches the game, goes to the main menu (where there is likely an image of "The Duke"), selects the "Credits" option, and (you guessed it) doesn't see any screen artifacts.  If he is an awesome Junior developer, he does this with the sound off and the sound on, then rejects the bug as 'unable to replicate'.  

During the next QA cycle, the same QA analyst reviews this bug (which makes sense under some circumstances).  The QA analyst sees that the bug still happens, and if he is a mediocre QA analyst, he updates the bug to say, "the Duke character sprite artifacts on the credits screen after completing gameplay - artifacts should not be present.".  A nice touch, but you can see where this is going.  The same dev gets the bug back, gets a little pissed because the bug was terribly documented the first time, and then builds the game with cheats on.  Because he can select the final level, and turn on invincibility, the dev walks through level 10, and kills the end boss with a pistol.  No artifacting.  bug rejected.  Now the QA Manager gets involved.  QA manager sees the bug going back and forth, sees the bug replicated by the QA analyst (but doesn't have time to watch the entire process), and reopens the bug, this time adding "no cheats".  The QA Manager, of course, doesn't know anything about the double-rocket-launcher, nor about the music changes during "The End Boss" sequence.  Back in development, now the Senior developer is looking at the bug.  Now you have an iteration where your senior developer is spending half a day playing through "The Duke" trying to get something to occur; this effort will most likely fail.  Bug rejected, and now you have your QA Manager spending an entire day playing "The Duke" to figure out how the bug should have been documented in the first place.  

As this cycle escalates, the ending point is a late product, days of time wasted for the QA analyst, Junior Developer, QA Manager, and Senior Developer.  Not to mention the fact that you will waste an hour or two of everyone's time having a meeting about how to write bugs and how to reject bugs.  

Don't do this.  As I mentioned, and I hope no one vehemently disagrees, an entry level employee is more likely to be able to fix that bug, than they are able to efficiently document it.  We can quibble over training exercises and QA Management vetting and other 'fail-safe' procedures.  But how many software developers out there can say that they have not experienced a situation like this?  

On the other side of this coin, if an entry level user is a junior developer, and they fail to fix a bug, it does not (realistically) waste anyone's time.  The build would go through QA anyway, and the build (in all likelihood) would end up back in development for review and/or updates.  The additional testing time from one QA analyst is meaningless when compared to the days of wasted time for multiple employees in our earlier situation.

A final issue I want to mention is that QA analysts who are in the right place, get better with time.  Because of the similarities in the developer and good QA analyst skill sets, often we find good QA analysts moving to development positions.  This is not because they are necessary better suited to development, but because it reduces hiring overhead.  Most businesses would rather spend virtually no time hiring, and get a developer that is known to be at least mediocre, than spend a great deal of time (and money) hiring, and have no idea how great a developer you will actually acquire.  Also, from the employee standpoint, this change almost always indicates a pay increase. Why wouldn't this transition happen?  

This is why I think the QA analyst should no longer be the first rung in the ladder of software development.  In the same way that we need to address the pay scale limitation of senior software developers versus management, I think the same battle is present (though further down the scale) between QA analysts and Junior/Mid-level developers.  At present, most organizations hold the QA analyst as a stagnating entry level position or a leap frog position for individuals without enough experience to get on their way to development.  If there were more focus on how much time and procedural efficiency is wasted by entry-level mistakes in QA, I think more organizations would spend time/money keeping good QA analysts where they are.  

Some notes:  I made several sweeping claims and a couple of targeted claims that can likely be countered with effective procedures and process improvements.  Given those facts, I concede that some organizations probably have managed to quarantine entry-level mistakes such that they have little effect on overall productivity, although none with which I have worked had efficient mechanisms for accomplishing this.  That said, I still believe that if QA analysts in those highly efficient organizations were more experienced, the process could be streamlined in ways that would make it much more efficient, because many of the checks/balances to accommodate possible entry-level QA mistakes could be modified/removed.  When your entry level is a junior developer, all of the checks/balances are in place.  QA analysts and lead/senior developers are in positions to to keep junior developers in check.  
