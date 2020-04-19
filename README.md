<h1>Live Project</h1>
<h2>Introduction</h2>
<p>For the last two weeks of my time at the tech academy, I worked with my peers in a team developing a full scale MVC/MVVM Web Application in C#. Working on a legacy codebase was a great learning oppertunity for fixing bugs, cleaning up code, and adding requested features. There were some big changes that could have been a large time sink, but we used what we had to deliver what was needed on time. I saw how a good developer works with what they have to make a quality product. I worked on several back end stories that I am very proud of. Because much of the site had already been built, there were also a good deal of front end stories and UX improvements that needed to be completed, all of varying degrees of difficulty. Everyone on the team had a chance to work on front end and back end stories. Over the two week sprint I also had the opportunity to work on some other project management and team programming skills that I'm confident I will use again and again on future projects.</p>
<p>Below are descriptions of the stories I worked on, along with code snippets and navigation links. I also have some full code files in this repo for the larger functionalities I implemented.</p>
<h2>Back End Stories</h2>
<ul>
  <li><a href="#sorting-network-table">Sorting Network Table</a></li>
  <li><a href="#meetup-api">Meetup API</a></li>
</ul>
<h3><a id="user-content-sorting-network-table" class="anchor" aria-hidden="true" href="#sorting-network-table"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>Sorting Network Table</h3>
<p>This page had two tables, one as part of the view, and one was a partial view. My task was to make the table on the partial view sortable by several different catagories. I wanted to do this without reloading the page.</p>
<pre><code>   public ActionResult _OutsideNetworking(string sortOrder)
    {
        List&lt;JPOutsideNetworking&gt; partialViewList = new List&lt;JPOutsideNetworking&gt;();
        partialViewList = db.JPOutsideNetworkings.ToList();

        ViewBag.NameSortParm = sortOrder == "studentName" ? "studentName_desc" : "studentName";
        ViewBag.PositionSortParm = sortOrder == "position" ? "position_desc" : "position";
        ViewBag.CompanySortParm = sortOrder == "company" ? "company_desc" : "company";
        ViewBag.LocationSortParm = sortOrder == "location" ? "location_desc" : "location";
        ViewBag.StackSortParm = sortOrder == "stack" ? "stack_desc" : "stack";

        switch (sortOrder)
        {
            default:
            case "studentName":
                partialViewList = partialViewList.OrderBy(x =&gt; x.Name).ToList();
                break;
            case "studentName_desc":
                partialViewList = partialViewList.OrderByDescending(x =&gt; x.Name).ToList();
                break;
            case "position":
                partialViewList = partialViewList.OrderBy(x =&gt; x.Position).ToList();
                break;
            case "position_desc":
                partialViewList = partialViewList.OrderByDescending(x =&gt; x.Position).ToList();
                break;
            case "company":
                partialViewList = partialViewList.OrderBy(x =&gt; x.Company).ToList();
                break;
            case "company_desc":
                partialViewList = partialViewList.OrderByDescending(x =&gt; x.Company).ToList();
                break;
            case "location":
                partialViewList = partialViewList.OrderBy(x =&gt; x.Location).ToList();
                break;
            case "location_desc":
                partialViewList = partialViewList.OrderByDescending(x =&gt; x.Location).ToList();
                break;
            case "stack":
                partialViewList = partialViewList.OrderBy(x =&gt; x.Stack).ToList();
                break;
            case "stack_desc":
                partialViewList = partialViewList.OrderByDescending(x =&gt; x.Stack).ToList();
                break;
        }

        return PartialView("_OutsideNetworking", partialViewList);
    }       
</code></pre>
<h3><a id="user-content-meetup-api" class="anchor" aria-hidden="true" href="#meetup-api"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>Meetup API</h3>
<p>I was tasked with fixing a parial view that displays information from Meetup.com. A previous developer had attempted the story with limited results. The meetup info could only be retrieved once per hour,my solution was to request meetup's info and save the results to a database. Whenever the API denied a request, the controller would use the latest data that had been saved.</p>
<pre><code>public PartialViewResult _MeetUpApi()
    {
        string[] meetupRequestUrls = {//url's removed for neatness};

        var events = new List&lt;JPMeetupEvent&gt;();
        try
        {
            var responseStrings = new List&lt;string&gt;();
            foreach (var url in meetupRequestUrls)
            {
                HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);
                request.AutomaticDecompression = DecompressionMethods.GZip | DecompressionMethods.Deflate;

                using (HttpWebResponse response = (HttpWebResponse)request.GetResponse())
                using (Stream stream = response.GetResponseStream())
                using (StreamReader reader = new StreamReader(stream))
                {
                    responseStrings.Add(reader.ReadToEnd());
                }
            }

            foreach (var str in responseStrings)
            {
                events.AddRange(ConvertMeetupStringToJPMeetupEvents(str));
            }
            events = FilterPastEvents(events);
            events = FilterDuplicateEvents(events);

            //remove old events from table
            db.JPMeetupEvents.RemoveRange(db.JPMeetupEvents);

            db.JPMeetupEvents.AddRange(events);

            db.SaveChanges();
        }

        //if there is a web exception, we need to load events from the database instead
        catch (WebException)
        {
            foreach (var meetupEvent in db.JPMeetupEvents)
            {
                events.Add(meetupEvent);
            }
                events = FilterPastEvents(events);
        }                                   
        return PartialView("_MeetUpApi", events);
    }
</code></pre>
<p>I had to write a helper function to convert the string that comes in from the API to our event model.</p>
<pre><code>    List&lt;JPMeetupEvent&gt; ConvertMeetupStringToJPMeetupEvents(string meetup)
    {
        var events = new List&lt;JPMeetupEvent&gt;();
        var meetupSB = new StringBuilder(meetup);            
        while (true)
        {
            var meetupEvent = new JPMeetupEvent();

            int nameIndex = meetupSB.ToString().IndexOf("\"name\"");
            if (nameIndex == -1) break;
            meetupSB.Remove(0, nameIndex + 8);
            int commaIndex = meetupSB.ToString().IndexOf(",");
            meetupEvent.JPEventName = meetupSB.ToString().Substring(0,commaIndex - 1);

            int dateIndex = meetupSB.ToString().IndexOf("\"local_date\"");                
            meetupSB.Remove(0, dateIndex + 14);
            commaIndex = meetupSB.ToString().IndexOf(",");
            var date = DateTime.Parse(meetupSB.ToString().Substring(0, commaIndex - 1));
            meetupEvent.JPEventDate = date;

            int linkIndex = meetupSB.ToString().IndexOf("\"link\"");              
            meetupSB.Remove(0, linkIndex + 8);
            commaIndex = meetupSB.ToString().IndexOf(",");
            meetupEvent.JPEventLink = meetupSB.ToString().Substring(0, commaIndex - 1);

            events.Add(meetupEvent);
        }


        return events;
    }
</code></pre>
<p>I also had to make sure the controller filtered out events that had already passed. Because multiple meetup groups sometimes post the same event, or would have several entries for a weekly event, I had to filter duplicate events and display the earliest one.</p>
<pre><code>    List&lt;JPMeetupEvent&gt; FilterPastEvents(List&lt;JPMeetupEvent&gt; events)
    {
        var filteredList = new List&lt;JPMeetupEvent&gt;();

        foreach (var meetupEvent in events)
        {
            if (meetupEvent.JPEventDate.CompareTo(DateTime.Now) &gt;= 0)
            {
                filteredList.Add(meetupEvent);
            }
        }

        return filteredList;
    }

    //will keep the earliest of the duplicates
    List&lt;JPMeetupEvent&gt; FilterDuplicateEvents(List&lt;JPMeetupEvent&gt; events)
    {
        var filteredEvents = new List&lt;JPMeetupEvent&gt;();
        var eventNames = new List&lt;string&gt;();
        foreach (var meetupEvent in events)
        {
            if (!eventNames.Contains(meetupEvent.JPEventName))
            {
                eventNames.Add(meetupEvent.JPEventName);
                filteredEvents.Add(meetupEvent);
            }
        }
        return filteredEvents;
    }
</code></pre>
<p><em>Jump to: <a href="#front-end-stories">Front End Stories</a>, <a href="#back-end-stories">Back End Stories</a>, <a href="#other-skills-learned">Other Skills</a>, <a href="#live-project">Page Top</a></em></p>
<h2><a id="user-content-front-end-stories" class="anchor" aria-hidden="true" href="#front-end-stories"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>Front End Stories</h2>
<ul>
<li><a href="#button-sizing-bug">Button Sizing Bug</a></li>
</ul>
<h3><a id="user-content-button-sizing-bug" class="anchor" aria-hidden="true" href="#button-sizing-bug"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>Button Sizing Bug</h3>
<p>This story was to fix an issue where buttons on the navbar would get smaller when clicked. It took some time to realize the font size was actually staying the same, and it was the font that was changing. From there, I just changed the a:active selector for those elements on the style sheet to use the same font.</p>
<p><em>Jump to: <a href="#front-end-stories">Front End Stories</a>, <a href="#back-end-stories">Back End Stories</a>, <a href="#other-skills-learned">Other Skills</a>, <a href="#live-project">Page Top</a></em></p>
<h2><a id="user-content-other-skills-learned" class="anchor" aria-hidden="true" href="#other-skills-learned"><svg class="octicon octicon-link" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z"></path></svg></a>Other Skills Learned</h2>
<ul>
<li>Working with a group of developers to identify front and back end bugs to the improve usability of an application</li>
<li>Improving project flow by communicating about who needs to check out which files for their current story</li>
<li>Learning new efficiencies from other developers by observing their workflow and asking questions</li>
<li>Practice with team programming/pair programming when one developer runs into a bug they cannot solve
<ul>
<li>One of the developers on the team was having trouble with the JavaScript function being called to increment and decrement the likes on a page and myself and two others on the team sat with him and had him talk through what he had done so far. I asked questions about different ways to approach it until we found where it was broken and what needed to be fixed.</li>
<li>When a user requests a friendship there is supposed to be a pending notification displayed. One of the other developers was hitting a wall while working on this story when he discovered the functionality was working four different ways across the application. I sat with him and we talked through the process of each JavaScript function being called. We discovered there were multiple functions by the same name being loaded, so we simplified the code down to just one function. Clicking the button would now work from the nav drop-down but not on a specific page. I realized that the page was populating two different spans with the same ID and these were what was being targeted by the JavaScript function. So we needed to make that user-specific element identifier a class and target the class instead so that a change in either place would affect both.</li>
</ul>
</li>
</ul>
<p><em>Jump to: <a href="#front-end-stories">Front End Stories</a>, <a href="#back-end-stories">Back End Stories</a>, <a href="#other-skills-learned">Other Skills</a>, <a href="#live-project">Page Top</a></em></p>
