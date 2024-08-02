# **SOLID**

## Purpose of SOLID

The SOLID principles are five object-oriented design principles that basically have the following objectives:

- Make the code more understandable, clear, and concise;
- Make the code more flexible and tolerant to changes;
- Increase the adherence of the code to object-oriented principles.

## What is SOLID?

SOLID is an acronym for each of the five principles that are part of this group:

### SRP - Single Responsibility Principle

A component should have only one reason to change, meaning it should have a single responsibility. This means a component should perform only one specific function or action.

- Encourages cohesion, making the code easier to maintain and understand.
- Reduces coupling between components.

```jsx
// Example of a component with a single responsibility
const Calculator = () => {
  const add = (a, b) => a + b;
  const subtract = (a, b) => a - b;

  return (
    <div>
      <h2>Calculator</h2>
      <p>Add: {add(2, 3)}</p>
      <p>Subtract: {subtract(5, 2)}</p>
    </div>
  );
};

export default Calculator;
```

### OCP - Open/Closed Principle

Software entities should be open for extension but closed for modification. This implies that the behavior of a component can be extended without altering its source code.

- Encourages the use of abstractions, higher-order components, and hooks.
- Helps in code reuse and system scalability.

```jsx
// Example using higher-order components to add functionalities
const withAreaCalculation = (WrappedComponent) => {
  return (props) => {
    const calculateArea = () => {
      if (props.shape === 'square') {
        return props.side * props.side;
      } else if (props.shape === 'circle') {
        return Math.PI * props.radius * props.radius;
      }
    };

    return <WrappedComponent calculateArea={calculateArea} {...props} />;
  };
};

const Shape = ({ shape, calculateArea }) => (
  <div>
    <h2>{shape}</h2>
    <p>Area: {calculateArea()}</p>
  </div>
);

const Square = withAreaCalculation(Shape);
const Circle = withAreaCalculation(Shape);

export { Square, Circle };
```

### LSP - Liskov Substitution Principle

Subtypes must be substitutable for their base types without altering the program's functionality. In other words, if S is a subtype of T, then objects of type T may be replaced with objects of type S without affecting the program's functionality.

- Ensures that subclasses follow the contract established by their superclasses.
- Helps in the correct use of inheritance and polymorphism.

```jsx
// Example of using composition following the LSP
const Rectangle = ({ width, height }) => {
  const area = width * height;
  return <p>Area: {area}</p>;
};

const Square = ({ side }) => {
  return <Rectangle width={side} height={side} />;
};

export { Rectangle, Square };
```

### ISP - Interface Segregation Principle

Many specific interfaces (or props) are better than one general-purpose interface. Components should not be forced to depend on props they do not use.

- Avoids "fat" interfaces with many props not used by all components.
- Promotes cohesion and granularity in interfaces.

```jsx
// Example of props segregation
const RemoteWork = ({ videoConference }) => (
  <div>
    <button onClick={videoConference}>Start Video Conference</button>
  </div>
);

const LocalWork = ({ writeDocument, editDocument }) => (
  <div>
    <button onClick={writeDocument}>Write Document</button>
    <button onClick={editDocument}>Edit Document</button>
  </div>
);

const Programmer = () => {
  const videoConference = () => {
    // Specific implementation for video conferencing
  };

  const writeDocument = () => {
    // Specific implementation for writing a document
  };

  const editDocument = () => {
    // Specific implementation for editing a document
  };

  return (
    <div>
      <RemoteWork videoConference={videoConference} />
      <LocalWork writeDocument={writeDocument} editDocument={editDocument} />
    </div>
  );
};

export default Programmer;
```

### DIP - Dependency Inversion Principle

High-level modules should not depend on low-level modules; both should depend on abstractions. This means that abstractions should not depend on details, but details should depend on abstractions.

- Uses inversion of control and dependency injection to promote flexibility and extensibility.
- Helps in writing more testable and loosely coupled code.

```jsx
import React, { createContext, useContext } from 'react';

// Create a Notifier context
const NotifierContext = createContext();

const EmailNotifier = {
  notify: () => console.info('User notified via email'),
};

const User = () => {
  const notifier = useContext(NotifierContext);
  return <button onClick={notifier.notify}>Notify User</button>;
};

const App = () => (
  <NotifierContext.Provider value={EmailNotifier}>
    <User />
  </NotifierContext.Provider>
);

export default App;
```

Conclusion about SOLID
The SOLID principles offer valuable guidelines for developers, helping to create more readable, flexible, and maintainable code. By understanding and applying these principles, developers can build more robust and scalable systems.