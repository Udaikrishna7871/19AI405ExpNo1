<h1>ExpNo 1 :Developing AI Agent with PEAS Description</h1>
<h3>Name: UDAI KRISHNA R </h3>
<h3>Register Number: 212224060288</h3>


<h3>AIM:</h3>
<br>
<p>To find the PEAS description for the given AI problem and develop an AI agent.</p>
<br>
<h3>Theory</h3>
<h3>Medicine prescribing agent:</h3>
<p>Such this agent prescribes medicine for fever (greater than 98.5 degrees) which we consider here as unhealthy, by the user temperature input, and another environment is rooms in the hospital (two rooms). This agent has to consider two factors one is room location and an unhealthy patient in a random room, the agent has to move from one room to another to check and treat the unhealthy person. The performance of the agent is calculated by incrementing performance and each time after treating in one room again it has to check another room so that the movement causes the agent to reduce its performance. Hence, agents prescribe medicine to unhealthy.</p>
<hr>
<h3>PEAS DESCRIPTION:</h3>
<table>
  <tr>
    <td><strong>Agent Type</strong></td>
    <td><strong>Performance</strong></td>
     <td><strong>Environment</strong></td>
    <td><strong>Actuators</strong></td>
    <td><strong>Sensors</strong></td>
  </tr>
    <tr>
    <td><strong>Medicine prescribing agent</strong></td>
    <td><strong>Treating unhealthy, agent movement</strong></td>
     <td><strong>Rooms, Patient</strong></td>
    <td><strong>Medicine, Treatment</strong></td>
    <td><strong>Location, Temperature of patient</strong></td>
  </tr>
</table>
<hr>
<H3>DESIGN STEPS</H3>
<h3>STEP 1:Identifying the input:</h3>
<p>Temperature from patients, Location.</p>
<h3>STEP 2:Identifying the output:</h3>
<p>Prescribe medicine if the patient in a random has a fever.</p>
<h3>STEP 3:Developing the PEAS description:</h3>
<p>PEAS description is developed by the performance, environment, actuators, and sensors in an agent.</p>
<h3>STEP 4:Implementing the AI agent:</h3>
<p>Treat unhealthy patients in each room. And check for the unhealthy patients in random room</p>
<h3>STEP 5:</h3>
<p>Measure the performance parameters: For each treatment performance incremented, for each movement performance decremented</p>
<h3>PROGRAM:</h3>

```python
import random
import time

class Thing:
    """This represents any physical object that can appear in an Environment."""

    def is_alive(self):
        """Things that are 'alive' should return true."""
        return hasattr(self, "alive") and self.alive

    def show_state(self):
        """Display the agent's internal state. Subclasses should override."""
        print("I don't know how to show_state.")


class Agent(Thing):
    """An Agent is a subclass of Thing."""

    def __init__(self, program=None):
        self.alive = True
        self.performance = 0
        self.program = program

    def can_grab(self, thing):
        """Return True if this agent can grab this thing."""
        return False


def TableDrivenAgentProgram(table):
    """
    This agent selects an action based on the percept sequence.
    It is practical only for tiny domains.
    """
    percepts = []

    def program(percept):
        percepts.append(percept)
        action = table.get(tuple(percepts))
        return action

    return program


room_A, room_B = (0, 0), (1, 0)  # The two locations for the Doctor to treat


def TableDrivenDoctorAgent():
    """Tabular approach towards hospital function."""
    table = {
        ((room_A, "healthy"),): "Right",
        ((room_A, "unhealthy"),): "treat",
        ((room_B, "healthy"),): "Left",
        ((room_B, "unhealthy"),): "treat",
        ((room_A, "unhealthy"), (room_A, "healthy")): "Right",
        ((room_A, "healthy"), (room_B, "unhealthy")): "treat",
        ((room_B, "healthy"), (room_A, "unhealthy")): "treat",
        ((room_B, "unhealthy"), (room_B, "healthy")): "Left",
        ((room_A, "unhealthy"), (room_A, "healthy"), (room_B, "unhealthy")): "treat",
        ((room_B, "unhealthy"), (room_B, "healthy"), (room_A, "unhealthy")): "treat",
    }
    return Agent(TableDrivenAgentProgram(table))


class Environment:
    """Abstract class representing an Environment."""

    def __init__(self):
        self.things = []
        self.agents = []

    def percept(self, agent):
        raise NotImplementedError

    def execute_action(self, agent, action):
        raise NotImplementedError

    def default_location(self, thing):
        return None

    def is_done(self):
        return not any(agent.is_alive() for agent in self.agents)

    def step(self):
        if not self.is_done():
            actions = []
            for agent in self.agents:
                if agent.alive:
                    actions.append(agent.program(self.percept(agent)))
                else:
                    actions.append("")
            for (agent, action) in zip(self.agents, actions):
                self.execute_action(agent, action)

    def run(self, steps=1000):
        for step in range(steps):
            if self.is_done():
                return
            self.step()

    def add_thing(self, thing, location=None):
        if not isinstance(thing, Thing):
            thing = Agent(thing)
        if thing in self.things:
            print("Can't add the same thing twice")
        else:
            thing.location = (location if location is not None else self.default_location(thing))
            self.things.append(thing)
            if isinstance(thing, Agent):
                thing.performance = 0
                self.agents.append(thing)

    def delete_thing(self, thing):
        try:
            self.things.remove(thing)
        except ValueError as e:
            print(e)
            print(" in Environment delete_thing")
            print(" Thing to be removed: {} at {}".format(thing, thing.location))
            print(" from list: {}".format([(thing, thing.location) for thing in self.things]))
        if thing in self.agents:
            self.agents.remove(thing)


class TrivialDoctorEnvironment(Environment):
    """
    This environment has two locations, A and B.
    Each can be unhealthy or healthy.
    """

    def __init__(self):
        super().__init__()
        self.status = {
            room_A: random.choice(["healthy", "unhealthy"]),
            room_B: random.choice(["healthy", "unhealthy"]),
        }

    def thing_classes(self):
        # FIX 1: was misspelled as "TableDrivenDocterAgent"
        return [TableDrivenDoctorAgent]

    def percept(self, agent):
        """Returns the agent's location and the location's status."""
        return agent.location, self.status[agent.location]

    def execute_action(self, agent, action):
        """Change agent's location and/or location's status; track performance."""
        if action == "Right":
            agent.location = room_B
            agent.performance -= 1
        elif action == "Left":
            agent.location = room_A
            agent.performance -= 1
        elif action == "treat":
            tem = float(input("Enter your temperature: "))
            if tem >= 98.5:
                # FIX 2: was '==' (comparison) instead of '=' (assignment)
                self.status[agent.location] = "unhealthy"
                print("Medicine prescribed: paracetamol and anti-biotic (low dose)")
                agent.performance += 10
            else:
                # FIX 3: removed duplicate line that always overwrote status to "healthy"
                self.status[agent.location] = "healthy"

    # FIX 4: moved default_location INSIDE the class (it was defined at module level)
    def default_location(self, thing):
        return random.choice([room_A, room_B])


if __name__ == "__main__":
    agent = TableDrivenDoctorAgent()
    environment = TrivialDoctorEnvironment()
    environment.add_thing(agent)

    print("\tStatus of patients in rooms before treatment")
    print(environment.status)
    print("AgentLocation : {0}".format(agent.location))
    print("Performance : {0}".format(agent.performance))
    time.sleep(3)

    for i in range(2):
        environment.run(steps=1)
        print("\n\tStatus of patient in room after the treatment")
        print(environment.status)
        print("AgentLocation : {0}".format(agent.location))
        print("Performance : {0}".format(agent.performance))
        time.sleep(3)

```
OUTPUT:
<img width="1146" height="441" alt="image" src="https://github.com/user-attachments/assets/364dd577-edcf-4d94-a71b-86a9e0a1aba1" />

<h3>RESULT:</h3>
Thus the AI agent is developed successfully
