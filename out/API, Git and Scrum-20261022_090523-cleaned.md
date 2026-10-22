# Agile Method and Scrum

## What is Agile?

### Definition of Agile
Agile is a methodology for software development that emphasizes small, iterative steps with fast feedback cycles. Instead of building everything at once and releasing it at the end, agile teams deliver working software in short cycles (sprints) where each cycle produces a potentially shippable product increment.

### Code Example
```python
# This represents the concept of delivering small increments
def deliver_increment(sprint_length):
    """Simulate delivering an increment every sprint"""
    print(f"Delivering working software every {sprint_length} weeks")
    return "Working software"

# Example usage
increment = deliver_increment(2)
print(increment)  # Output: Working software
```

### Key Concepts
- **Small steps**: Breaking work into manageable chunks
- **Fast feedback**: Regular demonstrations to customers
- **Quick adaptation**: Ability to change direction quickly
- **Customer collaboration**: Working closely with stakeholders throughout development

### Why Agile Exists
Agile emerged because traditional "waterfall" methods were problematic for software projects. Waterfall involves sequential phases where you can't easily go back once a phase is completed. In software, requirements often change, and customers learn what they really want only when they see working examples.

### Why It Matters
Understanding agile is crucial for modern software development because:
1. Most companies work in agile teams
2. It's a common topic in job interviews
3. You'll likely work in teams rather than alone
4. It helps manage changing requirements effectively

## Waterfall vs Agile

### Waterfall Method
The traditional approach where each phase of development happens sequentially:
- Requirements gathering
- System design
- Implementation
- Testing
- Deployment

### Key Characteristics
- Work flows "down" like water falling through steps
- Difficult to go back once requirements are set
- Changes become very expensive late in the process
- Risk is concentrated at the end of the project

### When Waterfall Works
Waterfall works well when:
- Requirements are known and won't change significantly
- You need a big plan upfront
- Examples: Building bridges, ships, medical devices requiring regulatory approval

### Waterfall vs Agile Comparison
```python
# Waterfall approach - one big delivery at the end
def waterfall_approach():
    requirements = gather_requirements()
    design = create_design(requirements)
    build = implement_system(design)
    test = verify_system(build)
    deploy = release_product(test)
    return deploy

# Agile approach - multiple small deliveries
def agile_approach():
    for sprint in range(4):
        sprint_plan = plan_sprint(sprint)
        work = execute_work(sprint_plan)
        demo = show_demo(work)
        feedback = collect_feedback(demo)
        adjust_plan(feedback)
    return "Final working product"
```

## The Agile Manifesto

### Four Core Values
1. **Individuals and interactions over processes and tools**
   - People talking to each other solves more problems than perfect tools
2. **Working software over comprehensive documentation**
   - A working program says more than a 200-page document
3. **Customer collaboration over contract negotiation**
   - Work with customers as partners, not opponents
4. **Responding to change over following a plan**
   - This is the most misunderstood part - doesn't mean no planning, but prioritizing flexibility

### Twelve Principles
1. Deliver working software frequently (weeks rather than months)
2. Welcome changing requirements even late in development
3. Business people and developers work together daily
4. Build projects around motivated individuals with face-to-face communication
5. Working software is the primary measure of progress
6. Sustainable development at any pace
7. Continuous attention to technical excellence
8. Simplicity - maximizing the amount of work not done
9. Self-organizing teams
10. Regular reflection and adjustment
11. Regular delivery of working software
12. Responding to change over following a plan

### Important Clarification
The manifesto doesn't say "no to" documentation or planning. It says both sides have value, but when you must choose, the left side wins. For example, you still document what's useful.

## Scrum Framework

### Overview
Scrum is the most popular agile framework with three roles, five events, and three artifacts. It uses short development cycles called sprints (usually 2 weeks).

### Three Roles in Scrum

#### Product Owner
- Decides what to build and in what order
- Talks to customers and users
- Owns the product backlog (one person, not a committee)
- Prioritizes items based on business value

#### Scrum Master
- Ensures scrum works properly
- Coaches the team
- Runs or helps with events when needed
- Removes obstacles that block the team
- **Important**: Not a project manager or boss who assigns tasks

#### Developers (Team)
- Build the product
- Include programmers, testers, designers, and other contributors
- Decide how to do the work themselves
- Work together daily, not just at start/end meetings

### Key Characteristics of Scrum Teams
- Small teams (usually 10 people or fewer)
- Self-organizing - team decides how to accomplish tasks
- Daily collaboration
- Fixed-length sprints (typically 2 weeks)

### Sprint Process
1. **Sprint Planning**: Team picks items from product backlog to complete in sprint
2. **Daily Scrum**: 15-minute daily meetings where developers check progress toward sprint goal
3. **Sprint Review**: Demo of completed work to stakeholders for feedback
4. **Sprint Retrospective**: Team discusses how they worked, not what was built

### Sprint Goal
Each sprint has a clear goal that defines "why" the sprint exists:
- Example: "Customers can search for products"
- The goal doesn't change during the sprint
- Details can change as team learns more

### Sprint Length
- Fixed duration (typically 2 weeks)
- No extensions even if nearly complete
- What isn't finished goes back to product backlog
- This rhythm makes teams predictable and reliable

## Scrum Events

### Sprint Planning
- Team answers three questions:
  1. Why is this sprint valuable? (Sprint goal)
  2. What can we finish? (Items from product backlog)
  3. How will we do it? (Breaking items into tasks)

### Daily Scrum (Daily Stand-up)
- Every day for 10-15 minutes
- Same time and place every day to make it a habit
- Developers check progress toward sprint goal
- Common questions:
  - What did I do yesterday?
  - What will I do today?
  - Is anything blocking me?

### Sprint Review
- At end of sprint
- Team shows working product to stakeholders
- Not a slideshow, but actual working demo
- Discussion about what to do next

### Sprint Retrospective
- Last event in sprint
- Focuses on how the team worked, not what was built
- Questions: What went well? What didn't? What will change?

## Scrum Artifacts

### Product Backlog
- List of everything that might be built
- Items ordered by value (most important at top)
- Detailed descriptions of each item
- Owned by product owner
- Never complete - constantly evolving

### Sprint Backlog
- Items selected for current sprint plus the plan
- What the team will do to achieve sprint goal
- Owned by developers
- Contains tasks broken down from selected items

### Increment (Working Product)
- New, potentially shippable piece of product
- Each sprint produces a working increment
- Must meet definition of done

## User Stories

### Definition
User stories are the standard way to write items in the product backlog. They follow a specific format:
```
As a [user type], I want [functionality] so that [reason].
```

### Example
```
As a customer, I want to filter products by category so that I can find shoes faster.
```

### Why This Format?
- Forces thinking about user and reason
- Shows what needs to be built
- Clarifies the "why" behind features
- Helps developers make good technical decisions

### Story Size Guidelines
- Should be small enough to complete in one sprint
- If too big, it's called an epic
- Epics should be split into smaller stories
- Each story should be estimable and testable

## Estimation Techniques

### Story Points
- Relative sizing method (not time-based)
- Often uses Fibonacci sequence: 1, 2, 3, 5, 8, 13
- Helps teams compare size of work rather than estimate hours
- Teams learn their velocity (points completed per sprint)

### Planning Poker
- Team members use cards with numbers
- Everyone reveals cards simultaneously
- Prevents influence from others
- Encourages discussion and knowledge sharing

### Velocity
- How many story points a team usually completes in a sprint
- Used for planning future sprints
- **Important**: Velocity is for the team, not comparing teams
- Points mean different things in different contexts

## Backlog Refinement

### Definition
Ongoing activity where the product owner and developers keep the backlog in good shape:
- Review items near top of backlog
- Split big items into smaller ones
- Add details and acceptance criteria
- Estimate items when appropriate

### Benefits
- Makes sprint planning quicker and more effective
- Ensures top items are ready, clear, small, and estimated
- Prevents long, painful planning meetings

## Kanban Method

### Overview
Kanban comes from Toyota's manufacturing system and focuses on continuous flow:
1. Make work visible on a board with columns (To Do, In Progress, Review, Done)
2. Limit Work in Progress (WIP) - maximum number of items in progress at once
3. Manage flow - pull next item when current one is complete

### Key Principles
- **Limit WIP**: If 3 items in progress, no new work started until one completes
- **Continuous flow**: Work moves through system continuously
- **No fixed sprints**: Work flows as it arrives

### When to Use Kanban
- Unpredictable work (support teams, bug fixes)
- Maintenance and operations teams
- When you can't plan ahead (like 2 weeks)

### Comparison: Scrum vs Kanban

| Aspect | Scrum | Kanban |
|--------|-------|---------|
| Rhythm | Fixed sprints | Continuous flow |
| Roles | Product Owner, Scrum Master, Developers | No formal roles |
| Sprint Goal | Fixed during sprint | Can change at any time (before work starts) |
| WIP Limits | By sprint capacity | By column limits |
| Planning | Sprint planning meetings | Ongoing refinement |

### Hybrid Approach
Many teams use a combination:
- Scrum with Kanban board and WIP limits
- Known as "ScrumBan"
- Combines benefits of both approaches

## Common Mistakes in Agile Implementation

### Daily Scrum Issues
1. **Status reports to manager**: Should be team-to-team communication
2. **Problem-solving meetings**: Should be kept short, only involved people

### Retrospective Problems
- Skipping retrospectives because "we're busy"
- Team stops caring about improvement

### Product Owner Issues
- Never available for the team
- Developers guess what to build instead of asking questions

### Story Size Problems
- Stories too big (nothing gets finished)
- All stories at 80% complete at sprint end

### Sprint Interference
- Pushing new urgent work into ongoing sprints
- Sprint goals never met

### "Scrum-but" Syndrome
- Doing scrum but without key elements like:
  - No product owner
  - Skipping reviews
  - Skipping retrospectives
  - Removing feedback mechanisms

## Tools Used in Agile Teams

### Popular Tools
1. **Jira**: Most common enterprise tool
2. **Azure DevOps**: Microsoft tools
3. **Trello**: Simple, free for small teams
4. **GitHub Projects**: Integrated with code repositories
5. **Whiteboards**: Physical sticky notes still widely used

### Free Kanban Tool
- Many teams use online kanban tools like Trello or similar platforms

## Real-World Application

### When to Choose Scrum vs Kanban
If you're starting a job that fixes urgent customer problems every day:
- **Choose Kanban** because work is unpredictable and urgent
- Scrum with fixed sprints wouldn't work for this type of work

### Benefits in Practice
1. **Daily Stand-up**: Prevents getting stuck for days without communication
2. **Team collaboration**: Better coordination than working alone
3. **Regular feedback**: Catch issues early rather than at the end
4. **Continuous improvement**: Regular retrospectives help teams get better

## Summary

### Key Takeaways
- Agile = small steps + fast feedback + welcoming change
- Manifesto has 4 values and 12 principles
- Scrum has 3 roles, 5 events, 3 artifacts
- Product Owner, Scrum Master, Developers
- Sprint Planning, Daily Scrum, Sprint Review, Sprint Retrospective
- Product Backlog, Sprint Backlog, Increment
- Kanban uses board, WIP limits, continuous flow

### Practical Applications
1. **Job interviews**: Expect questions about sprint, roles, artifacts
2. **Work environment**: Use daily scrum for communication
3. **Personal projects**: Simple board with To Do, Doing, Done columns works well
4. **Team learning**: Focus on continuous improvement through retrospectives

### Remember
The most important thing is that teams keep learning and improving, regardless of which specific method they use. The names (Scrum, Kanban) are less important than the principles of collaboration, feedback, and continuous improvement.