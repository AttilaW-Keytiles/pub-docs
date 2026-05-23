# Preface

We had the need to add massive features to the System-UI (Keytiles) and we needed it fast: Reports.

This involved both backend (Golang) + frontend (React app) work.

I decided to put myself on learning curve in terms of how could things like this done with AI agents maintaining a right balance between complete vibe coding (stupid and risky) and everything written by hand.

Let me document the workflow quickly in this file where did I arrive to during the past ~2 months.

I used [Cursor IDE](https://cursor.com/get-started) for this.

# Important findings

As I was working with the AI agents I did not want to give up constantly reviewing what the agent did.
For this I broke down complex tasks to step-by-step approach. Always asking the agent to do small steps at a time.

This way I had the chance to review small changes it made in the code and apply correction steps immediately when agent started to do stupid things.
It did... A lot... Especially architecture decisions needed corrections constantly. :-)

And after some iterations - which you typically do in the chat windows - I realized: fuck now we did 15-20 iterations in one chat window... And as context size getting exceeded and I need to open a new chat window, all these things are lost.
Saving complete chats into files (which I did in the beginning) seemed to be a no go and not useful (nobody will read them again)

However, it also turned out that agents are just as good as as accurate my specifications are...

I also realized that when I start a new chat window (either because weeks were passed or the context got full) I kept myself repeating summarizing (tried to be short) what we did so far. Referring in API contract links, pointing to files / folders bla bla bla.

This was crazy and only worked when I started the chat window immediately after the full context one and I remembered precisely what summary to give to continue the work the best and most efficient way...

This is not sustainable!

I also found this youtube video in which it looks the guy actually arrived to something very very similar to what I have arrived to:
[https://www.youtube.com/watch?v=h0hdaHPKDdI](https://www.youtube.com/watch?v=h0hdaHPKDdI) - "What 6 months of AI coding did to my dev team"

# Current workflow

Withing the git repo I created two folders:
 * `docs` - I ask agent to create feature / module etc documentation .md files here - versioned! (Semantic but only major.minor - no need for patch for docs)
 * `cursor-prompts` and within this subfolders of release versions (but this is optional).
 
And then I do the following now:

## Step 1 - preparation

 1. I figure it out what feature / increment I want to work on. I try to be small!
 2. Then I check the "docs" folder if I have respective module / class / feature docs there describing how the system works NOW! If not then I ask AI agent to document this/that for me there how does it work. I usually pre-create empty files there as placeholders for the docs. And it is really a good idea to phrase some rules/best practices how these should look like / work - see [Tips - rules / best practices](#document-rules--best-practices-into-standalone-md-files)

So we have a clean starting state!

## Step 2

It's time to specify what I want!

I put agent to "Plan" mode and we discuss the requirements. We are working on "specification" together.

In this step I reuse files from [step #1](#step-1---preparation) asking the agent to read this/that through to pick up context. 
After I described what I want (best way I can, more details is the better!) I typically write

```
Feel free to ask calrification questions if there are any!
```
as this helps a lot, it really does!

When plan looks good, in the "cursor-prompts" folder (or subfolders - up to you) I create a new blank file "feature-xxx-vX.Y.md" (version is an increment of course compared to prev versions) and ask the agent to document the plan there.

## Step 3

Then we implement with the agent the plan (specification) and do iterations / code reviews etc. As much as needed.

Check [Tips](#tips) here! There are a few useful stuff there...

## Step 4

Finally, once the implementation looks good, it is time to document the changes!

I create blank file(s) within "docs" folder - postfixed versions of modules / features - and ask the agent to document what we have changed and how does that stuff work now.

Tip: see [Document rules / best practices](#document-rules--best-practices-into-standalone-md-files) - really helpful...

```
Now let's document a bit!

I created a new file @docs/kt_corequeryapi_helpers/EventCountResponseDecorator-v1.3.md 
Please refer in @cursor-prompts-docs/kt_corequeryapi_helpers/feature-EventCountResponseDecorator-v1.3-plan.md here as change plan.

Please read @docs/rules-and-best-practices.md and make sure the docs complies with that.
```

## Step 5

Repeat - for every feature. Until the release is OK to be closed and released

## Step 6

Do the release, merge back, bla bla bla
And start the whole thing again.

# Tips

## Master the Git workflow!

This helps a lot because if any step goes wrong no need to waste agent tokens to ask him to revert :-P Here is what I do:

1. Create a usual feature branch - like "release-2.7.0" - for the increment in the product. And we will develop several new features for this release, so:
2. Fork out a small(er) feature branch from the "release-2.7.0" - when I start "Step #2"
3. Make commits often while in interations - during "Step #3". If anything is wrong, I just revert back. I also can review file changes easily this way, small increments.
4. Once the sub-feature is done - at "Step #4" - I merge back to release branch: "release-2.7.0"
5. finally, at "Step #6" as usual, we merge back the version branch "release-2.7.0" to main. Done.

## Follow TDD

Another good habit I found that when we are done with the plan (Step #2), first I ask the agent to create test cases.
And do NOT focus on the implementation. 

Of course to let the agent write test cases and if these are unit tests, method signatures must be present.
I often prepared method signatures myself to the right places but missing body. Or I can also ask the agent to "OK add methods but do not implement them yet".

He will complain a bit that tests will not work bla bla but you can ignore that :-)

And then, go through and review the test cases carefully!!!
In some cases you can spot misunderstandings, hallucinated more requirements (which you never said) etc this way.

If anything like that is spotted you can also ask the agent to fine tune the plan because "hey this never was a requirement". Happened to me few times...

Once test cases look good, only after that you let the agent to do the implementation.

Why?
Because I noticed that agents can behave like bad programmers... They love to modify test cases the way they are passing validating a faulty code :-P. Just like bad developers do...

You can even say

```
Test cases really looks good now! Let's fix them and please do NOT modify them unless asked. Even if we do code changes. Frozen test cases. Understood?
```

## Careful around tests!!

When you ask the agent to do code changes it really loves to immediatelly discover and change existing test code to match the changed implementation.

This is baaad! Because if you overlook this then bingo: tests are not protecting you against bad implementation anymore.
To be honest he loves to work like a bad engineer in this sense... Making tests green at all cost :-P

So what I do when I ask agent to implement something - unless in the increment I follow [TDD already](#follow-tdd) -  is that I explicitly tell him:

```
Do not modify test cases for now - just the code. We deal with tests later.
```

## Document rules / best practices into standalone .md files

When we work with the agent on code or on docs - see [Step 1](#step-1---preparation) or [Step 4](#step-4) - it is really handy to sit down once and craft an .md file in which you simply write down (try to be short, concise!) rules and best practices.

I do it for:
* coding rules
* documentation rules

but you can do it literally for anything.

Then you can simply tell the agent "hey look at the rules <here - in this file> and keep them" so you dont need to manually repeat yourself again and again.

**Note:** environments like Cursor absolutely allows you to put in rules into some folder like ".cursor/rules" or similar where files are always automatically picked up for each context.

This is great but so far I did not do this. Because simply I do not feel mature enough my rules and I really do not want to end up so far in a state something lives there "under the hood" and I might easily overlook this automated way stuff then do not understand what my agent does and why...
So for now I just refer to these manually in prompts.


# Observations / Conclusions

## Feature delivery speed

Using this agent based coding does NOT speed you up 500% for sure. :-)

But it happened many times to me that agent spotted things I easily overlooked. Small things, rarely serious but if I would have written the code myself very likely these small overlooks would have lead to small bugs. Which then I need to hunt down => takes time each! And these small times accumulating in a feature development so I am pretty sure I saved lots of time and and "WTF???" frustrations because of this.

Using the agent to craft test cases (but really review them!! See "Follow TDD" section above) also saves lots of time. Even with thorough review as surprisingly it is able to make pretty good recommendations for test cases, roughly 80% precision.

As a gut feeling: features would take me to code traditionally 2 days now I can produce in 1 day AND with better quality. So realistically speed increase is around 2x.

BUT! 

Don't forget that this is just about writing the code which I do alone! No discussions needed with others, no contract making is in the loop. No big overarching topics and design. I am just one developer, alone. Already clear what I want (more or less) in my own scope / product. And we all know that feature delivery also requires alignment with others right? Code writing itself is around 30-40% of the full work. And agent is just speeding up that - nothing else.

## Architecture / Class design decisions made by AI

Not that great... You really need to keep your eye on even tiny details even on class or method level.

Take [Step 2](#step-2) very seriously!

If you do not correct the stupid decisions then it can esily lead to 10-15% stupid code, unnecessary private methods or simply poorly designed structures and functionality. It happened to me many many times. So experience is still needed a lot!

This means if you let AI run by itself after 4-5 development iterations you can easily end up in 100% more stupid code pieces which later quickly turns into a nightmare nobody will understand.
