# Agile Method and Scrum Overview

## What is Agile?

### Definition
Agile is a methodology for software development that emphasizes small, iterative steps and rapid feedback. Instead of building an entire product at once and releasing it at the end, agile teams deliver working software in short cycles (sprints) to get early feedback from customers.

### Code Example
```python
# This represents the concept of iterative development
def build_product_incrementally():
    # Start with basic features
    basic_features = ["login", "dashboard"]
    
    # Add more features in subsequent sprints
    enhanced_features = ["search", "filter", "user profiles"]
    
    # Continue improving based on feedback
    final_features = ["notifications", "analytics", "mobile responsive"]
    
    return final_features

# Example of how agile works - each sprint builds upon previous work
sprint_1 = build_product_incrementally()[:2]  # First two features
sprint_2 = build_product_incrementally()[2:4]  # Next two features  
sprint_3 = build_product_incrementally()[4:]   # Final features
```

### Key Points
- **Fast feedback**: Instead of waiting a year to see results, teams get feedback every few weeks
- **Small steps**: Work in short cycles (typically 2 weeks)
- **Customer involvement**: Customers see working software early and often
- **Adaptability**: Teams can adjust direction quickly based on new information

### Why It Matters
Agile methods are used by almost all developer jobs because they:
1. Reduce risk by finding problems early
2. Improve customer satisfaction through regular feedback
3. Allow teams to respond to changing requirements
4. Make projects more predictable and manageable

## The Waterfall Method (Traditional Approach)

### Definition
Waterfall is the traditional approach to software development where work flows in one direction from requirements through design, implementation, testing, and finally release. Once a phase is complete, you cannot go back.

### Code Example
```python
# Traditional waterfall approach - sequential phases
def waterfall_development():
    # Phase 1: Requirements
    requirements = gather_requirements()
    
    # Phase 2: Design
    design = create_design(requirements)
    
    # Phase 3: Implementation
    implementation = build_code(design)
    
    # Phase 4: Testing
    testing = test_software(implementation)
    
    # Phase 5: Release
    release = deploy_to_production(testing)
    
    return release
```

### Key Points
- **Sequential phases**: Each phase must be completed before the next begins
- **High upfront planning**: All requirements are defined at the start
- **Difficult to change**: Modifying requirements is expensive and time-consuming
- **Risk at end**: You don't know if the final product works until the very end

### Why Waterfall Isn't Always Wrong
Waterfall works well for projects where:
- Requirements are known and fixed (like building a bridge or ship)
- Changes are extremely costly
- There's a clear, unchanging scope

## The Agile Manifesto

### Definition
The Agile Manifesto is a 12-page document written in 2001 by 17 software developers that outlines four core values and 12 principles for agile development.

### Core Values
1. **Individuals and interactions** over processes and tools
2. **Working software** over comprehensive documentation  
3. **Customer collaboration** over contract negotiation
4. **Responding to change** over following a plan

### Code Example
```python
# Demonstrating the values of the manifesto
class AgileTeam:
    def __init__(self):
        self.values = {
            "individuals_interactions": "Communication happens directly between team members",
            "working_software": "Focus on delivering working features rather than documentation",
            "customer_collaboration": "Work closely with customers throughout development",
            "responding_to_change": "Adapt quickly to new requirements"
        }
    
    def demonstrate_manifesto(self):
        # Show that working software beats documentation
        working_feature = self.build_working_software()
        documentation = self.create_documentation()
        
        return f"Working software ({working_feature}) is valued more than documentation ({documentation})"
```

### Key Principles
1. Deliver working software frequently (every few weeks)
2. Welcome changing requirements even late in development
3. Developers and business people work together daily
4. Build projects around motivated individuals
5. Face-to-face conversation is the best form of communication
6. Working software is the primary measure of progress
7. Sustainable development at any pace
8. Continuous attention to technical excellence
9. Simplicity is essential
10. Self-organizing teams
11. Regular reflection and adjustment

### Why It Matters
The manifesto represents a fundamental shift from rigid, process-heavy methods to flexible, people-oriented approaches that work better in the unpredictable world of software development.

## Scrum Framework

### Definition
Scrum is the most popular agile framework used by software teams. It provides specific roles, events, and artifacts to implement agile principles.

### The Scrum Team Structure
Scrum teams consist of three main roles:
1. **Product Owner**: Decides what to build and in what order
2. **Scrum Master**: Ensures the team follows scrum practices
3. **Developers**: Build the product (programmers, testers, designers, etc.)

### Code Example
```python
# Scrum team structure example
class ScrumTeam:
    def __init__(self):
        self.product_owner = "Jane Smith"
        self.scrum_master = "John Doe" 
        self.developers = ["Alice", "Bob", "Charlie"]
        
    def daily_scrum(self):
        # Daily standup meeting format
        print("Daily Scrum:")
        for developer in self.developers:
            print(f"{developer}: What did I do yesterday? What will I do today? Any blockers?")
    
    def sprint_planning(self, sprint_goal):
        print(f"Planning sprint with goal: {sprint_goal}")
        # Team selects items from product backlog
        return "Items selected for this sprint"

# Example usage
team = ScrumTeam()
team.daily_scrum()
```

### Scrum Events (Sprint Cycle)
1. **Sprint**: Fixed time period (usually 2 weeks) where work is completed
2. **Sprint Planning**: Team decides what to build in the upcoming sprint
3. **Daily Scrum**: Short daily meeting (15 minutes max) for coordination
4. **Sprint Review**: Demo of completed work to stakeholders
5. **Sprint Retrospective**: Team reflects on how they worked

### Code Example
```python
# Sprint cycle example
class SprintCycle:
    def __init__(self, sprint_length=2):
        self.sprint_length = sprint_length  # weeks
        self.sprint_goal = ""
        self.completed_work = []
        
    def run_sprint_cycle(self):
        print("Starting sprint cycle...")
        self.sprint_planning()
        self.daily_scrums()
        self.sprint_review()
        self.sprint_retrospective()
        print("Sprint completed!")
        
    def sprint_planning(self):
        print("Sprint Planning: Selecting items for this sprint")
        self.sprint_goal = "Implement user authentication system"
        
    def daily_scrums(self):
        print("Daily Scrums: Team coordination meetings")
        
    def sprint_review(self):
        print("Sprint Review: Demo to stakeholders")
        
    def sprint_retrospective(self):
        print("Sprint Retrospective: Team reflection on process")

# Run a sprint cycle
sprint = SprintCycle()
sprint.run_sprint_cycle()
```

### Scrum Artifacts
1. **Product Backlog**: List of all features, bugs, improvements (prioritized)
2. **Sprint Backlog**: Items selected for current sprint plus implementation plan
3. **Increment**: Working product delivered at end of sprint

### Code Example
```python
# Scrum artifacts example
class ScrumArtifacts:
    def __init__(self):
        self.product_backlog = [
            {"id": 1, "priority": "High", "description": "User login feature"},
            {"id": 2, "priority": "Medium", "description": "Password reset functionality"},
            {"id": 3, "priority": "Low", "description": "Email notifications"}
        ]
        
        self.sprint_backlog = []
        self.increment = ""
        
    def create_sprint_backlog(self, items):
        self.sprint_backlog = items
        print("Sprint Backlog created:", [item['description'] for item in items])
        
    def build_increment(self, completed_features):
        self.increment = " ".join(completed_features)
        print("Increment built:", self.increment)

# Usage
artifacts = ScrumArtifacts()
artifacts.create_sprint_backlog([artifacts.product_backlog[0]])
```

### User Stories
User stories are the standard way to write items in the product backlog.

#### Format: As a [user], I want [feature] so that [reason]

### Code Example
```python
# User story format example
class UserStory:
    def __init__(self, user_type, feature, reason):
        self.user_type = user_type
        self.feature = feature
        self.reason = reason
        
    def format_story(self):
        return f"As a {self.user_type}, I want {self.feature} so that {self.reason}"
    
    def __str__(self):
        return self.format_story()

# Example stories for web shop
customer_story = UserStory("customer", "filter products by category", "find shoes faster")
staff_story = UserStory("shop staff", "view sales reports", "track inventory levels")

print(customer_story)
print(staff_story)
```

### Definition of Done (DoD)
A checklist that defines when work is truly complete.

### Code Example
```python
# Definition of Done example
class DefinitionOfDone:
    def __init__(self):
        self.checklist = [
            "Code has been reviewed by another developer",
            "All automated tests pass",
            "Code is merged into main branch",
            "Documentation has been updated",
            "Feature works as expected in staging environment"
        ]
        
    def is_done(self, work_item):
        # Check if all criteria are met
        return all(criteria in work_item['completed_criteria'] for criteria in self.checklist)
    
    def print_checklist(self):
        print("Definition of Done Checklist:")
        for i, item in enumerate(self.checklist, 1):
            print(f"{i}. {item}")

# Usage
dod = DefinitionOfDone()
dod.print_checklist()
```

## Story Points and Estimation

### Definition
Story points are a relative measure of how much effort a story requires. They use the Fibonacci sequence (1, 2, 3, 5, 8, 13) to represent increasing uncertainty.

### Code Example
```python
# Story point estimation example
class StoryEstimator:
    def __init__(self):
        self.fibonacci_scale = [1, 2, 3, 5, 8, 13]
        
    def estimate_story(self, story_size):
        if story_size <= 0:
            return "Too small to estimate"
        elif story_size > 13:
            return "Split into smaller stories"
        else:
            return f"Story estimated at {story_size} points"

# Example usage
estimator = StoryEstimator()
print(estimator.estimate_story(5))
print(estimator.estimate_story(20))  # Should suggest splitting
```

### Planning Poker
A technique where team members simultaneously reveal their estimates to avoid bias.

### Code Example
```python
# Simulate planning poker
import random

class PlanningPoker:
    def __init__(self, players):
        self.players = players
        self.cards = [1, 2, 3, 5, 8, 13]
        
    def reveal_estimates(self):
        estimates = []
        for player in self.players:
            estimate = random.choice(self.cards)
            estimates.append((player, estimate))
            print(f"{player}: {estimate} points")
        return estimates
    
    def discuss_and_reveal(self):
        print("Players reveal their cards simultaneously...")
        return self.reveal_estimates()

# Example
poker = PlanningPoker(["Alice", "Bob", "Charlie"])
estimates = poker.discuss_and_reveal()
```

## Backlog Refinement

### Definition
Backlog refinement is the ongoing process of reviewing and preparing items in the product backlog for future sprints.

### Code Example
```python
# Backlog refinement example
class BacklogRefinement:
    def __init__(self):
        self.backlog_items = [
            {"id": 1, "title": "User login", "status": "Needs refinement"},
            {"id": 2, "title": "Payment processing", "status": "Ready for sprint"},
            {"id": 3, "title": "Email notifications", "status": "Needs details"}
        ]
        
    def refine_backlog(self):
        print("Refining backlog items...")
        for item in self.backlog_items:
            if item['status'] == 'Needs refinement':
                item['status'] = 'Refined'
                item['details'] = "Added acceptance criteria"
                print(f"Item {item['id']} refined")
            elif item['status'] == 'Needs details':
                item['status'] = 'Ready for sprint'
                print(f"Item {item['id']} ready for sprint")

# Usage
refinement = BacklogRefinement()
refinement.refine_backlog()
```

## Kanban Method

### Definition
Kanban is a visual workflow management method that limits work in progress and optimizes flow.

### Code Example
```python
# Kanban board example
class KanbanBoard:
    def __init__(self):
        self.columns = {
            "To Do": [],
            "In Progress": [],
            "Review": [],
            "Done": []
        }
        
    def add_task(self, task, column="To Do"):
        self.columns[column].append(task)
        print(f"Added {task} to {column}")
        
    def move_task(self, task, from_column, to_column):
        if task in self.columns[from_column]:
            self.columns[from_column].remove(task)
            self.columns[to_column].append(task)
            print(f"Moved {task} from {from_column} to {to_column}")
            
    def display_board(self):
        for column, tasks in self.columns.items():
            print(f"{column}: {tasks}")

# Usage
board = KanbanBoard()
board.add_task("Implement login feature", "To Do")
board.add_task("Write unit tests", "To Do")
board.move_task("Implement login feature", "To Do", "In Progress")
board.display_board()
```

### Key Differences from Scrum
| Feature | Scrum | Kanban |
|---------|-------|--------|
| **Rhythm** | Fixed sprints | Continuous flow |
| **Roles** | Product Owner, Scrum Master, Developers | No specific roles |
| **Change Management** | Sprint goal stays fixed | Can change anytime |
| **Work Limiting** | Sprint capacity | WIP limits per column |

### When to Use Each
- **Scrum**: When building new products with regular feedback needed
- **Kanban**: When work comes unpredictably (support teams, bug fixes)

## Common Mistakes

### Code Example
```python
# Common scrum mistakes to avoid
class ScrumMistakes:
    def __init__(self):
        self.mistakes = [
            "Daily scrum becomes status report to manager",
            "Retrospective skipped due to being busy",
            "Product owner unavailable for questions",
            "Stories too big (not broken into smaller ones)",
            "New urgent work pushed into sprint"
        ]
        
    def identify_mistake(self, scenario):
        if "status report" in scenario.lower():
            return "Daily scrum mistake - should be team coordination not management update"
        elif "skipped" in scenario.lower():
            return "Retrospective mistake - reflection is key to improvement"
        elif "big stories" in scenario.lower():
            return "Story size mistake - should break into smaller items"
        else:
            return "Mistake identified - review scrum practices"

# Usage
mistakes = ScrumMistakes()
print(mistakes.identify_mistake("Daily scrum became status report"))
```

## Why It Matters for Developers

### Interview Preparation
Most developer jobs require knowledge of agile and scrum concepts because:
1. **Job interviews** often ask about sprint cycles, roles, and processes
2. **Real-world application** - you'll work in teams using these methods
3. **Career advancement** - understanding agile is essential for promotions

### Practical Application
Even as a solo developer or junior developer, you can apply agile concepts:
1. **Daily standups** - communicate progress and blockers
2. **Small steps** - break large tasks into manageable pieces
3. **Feedback loops** - share work early and often
4. **Continuous improvement** - reflect on what works and what doesn't

### Code Example
```python
# Practical agile application in personal projects
class PersonalAgileProject:
    def __init__(self):
        self.sprint_goal = ""
        self.tasks = []
        
    def daily_progress_update(self, task, progress):
        print(f"Task '{task}' is {progress}% complete")
        
    def sprint_review(self, completed_tasks):
        print("Sprint Review:")
        for task in completed_tasks:
            print(f"✓ Completed: {task}")
            
    def retrospective(self, improvements):
        print("Retrospective:")
        for improvement in improvements:
            print(f"Improvement: {improvement}")

# Usage
project = PersonalAgileProject()
project.daily_progress_update("User login", 75)
project.sprint_review(["User login", "Password reset"])
project.retrospective(["Use better error handling", "Improve documentation"])
```