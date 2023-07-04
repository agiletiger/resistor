# ![](./logo.svg) Resistor 


## A lightweight workflow orchestrator DYI guide
Resistor started as a joke when the VP of engineering proposed using Netflix Conductor and we thought that was an overkill and decided to think how we could implement that in house.


### Non functional requirements
A good orchestrator must at least have the have the following properties:

* observability
* versioning
* debugging past workflow executions
* supporting unit tests for steps activities
* queue management (optional)
* retries (optional)
* timers (optional)

### Functional requirements
- long running worflows 
- state management
- running different versions of a workflow concurrently
- define workflows variations

## Implementation

### Orchestrator responsibilities

- Knows how to send a message to every other module
- Knows which workflow should use upon receiving an event
- Store the current step for a workflow
- Generate a version aware continue token to continue the following step in the workflow

### Modules responsibilities
- Know how to send a message to the orchestrator

### Workflow definition example

At the root level we have diffferent namespaces, you can have the same workflow name in different namespaces (so different consumers can have different steps).

Inside each namespace the different events that will trigger a workklow are defined.

A workflow is comprised of a version and the steps array.
```javascript
{
  namespaceA: {
    event1: {
    version: 1,
      steps: [
        {
          module: "moduleA", 
          action: "actionOfA", 
          metadata: actionOfARules
        }
      ]
    }
  },
  namespaceB: {
    event1: {
      version: 1,
      steps: [
        {
          module: "moduleB", 
          action: "actionOfB", 
          metadata: actionOfBRules
        }
      ]
    }
  }
  namespaceC: {
    event1: {
      version: 1,
      steps: [
        {
          module: "moduleB", 
          action: "actionOfB", 
          metadata: actionOfBRules
        }, 
        {
          module: "moduleA", 
          action: "actionOfA", 
          metadata: actionOfARules
        }
      ]
    }
  }
}
```