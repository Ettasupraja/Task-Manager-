## AIM
To create a Task Manager app using React where users can add, delete, toggle, and filter tasks.
## STEPS

Create a React component called TaskManager.

Use useReducer to manage the task list (add, toggle, delete).

Use useState for input text and filter type.

Use useRef to focus the input box after adding a task.

Use useCallback to make event handlers efficient.

Use useMemo to filter tasks quickly based on filter type.

Render the tasks and buttons for adding, deleting, toggling, and filtering tasks.
## PROGRAM
```
import React, { useReducer, useRef, useMemo, useCallback, useState } from "react";

const ACTIONS = {
  ADD: "add",
  TOGGLE: "toggle",
  DELETE: "delete"
};

function taskReducer(tasks, action) {
  switch (action.type) {
    case ACTIONS.ADD:
      return [
        ...tasks,
        { id: Date.now(), text: action.payload.text, completed: false }
      ];
    case ACTIONS.TOGGLE:
      return tasks.map(task =>
        task.id === action.payload.id
          ? { ...task, completed: !task.completed }
          : task
      );
    case ACTIONS.DELETE:
      return tasks.filter(task => task.id !== action.payload.id);
    default:
      return tasks;
  }
}

export default function TaskManager() {
  const [tasks, dispatch] = useReducer(taskReducer, []);
  const [text, setText] = useState("");
  const [filterType, setFilterType] = useState("all");
  const inputRef = useRef();

  const handleAdd = useCallback(() => {
    if (text.trim() === "") return;
    dispatch({ type: ACTIONS.ADD, payload: { text } });
    setText("");
    inputRef.current.focus();
  }, [text]);

  const handleToggle = useCallback(id => {
    dispatch({ type: ACTIONS.TOGGLE, payload: { id } });
  }, []);

  const handleDelete = useCallback(id => {
    dispatch({ type: ACTIONS.DELETE, payload: { id } });
  }, []);

  const filteredTasks = useMemo(() => {
    switch (filterType) {
      case "completed":
        return tasks.filter(task => task.completed);
      case "uncompleted":
        return tasks.filter(task => !task.completed);
      default:
        return tasks;
    }
  }, [tasks, filterType]);

  return (
    <div style={{
      position: "fixed",
      top: 0,
      left: 0,
      right: 0,
      bottom: 0,
      backgroundColor: "#e6e6fa",
      display: "flex",
      justifyContent: "center",
      alignItems: "center",
      padding: "20px",
      overflow: "auto"
    }}>
      <div style={{
        padding: "35px",
        backgroundColor: "#ffffff",
        borderRadius: "15px",
        boxShadow: "0 10px 30px rgba(0,0,0,0.15)",
        width: "100%",
        maxWidth: "550px"
      }}>
        <h1 style={{ 
          textAlign: "center", 
          marginBottom: "30px",
          marginTop: "0",
          color: "#2c3e50",
          fontSize: "32px",
          fontWeight: "700"
        }}>📝 Task Manager</h1>
        
        <div style={{ 
          display: "flex", 
          gap: "10px",
          marginBottom: "25px"
        }}>
          <input
            ref={inputRef}
            type="text"
            value={text}
            onChange={e => setText(e.target.value)}
            onKeyPress={e => e.key === 'Enter' && handleAdd()}
            placeholder="What needs to be done?"
            style={{
              padding: "12px 15px",
              flex: 1,
              borderRadius: "8px",
              border: "2px solid #e0e0e0",
              fontSize: "15px",
              outline: "none",
              transition: "border-color 0.3s"
            }}
          />
          <button
            onClick={handleAdd}
            style={{
              padding: "12px 24px",
              borderRadius: "8px",
              border: "none",
              backgroundColor: "#4caf50",
              color: "#fff",
              cursor: "pointer",
              fontSize: "15px",
              fontWeight: "600",
              boxShadow: "0 2px 8px rgba(76,175,80,0.3)"
            }}
          >
            Add Task
          </button>
        </div>

        <div style={{ 
          display: "flex", 
          gap: "12px",
          marginBottom: "25px",
          justifyContent: "center",
          flexWrap: "wrap"
        }}>
          {["all", "completed", "uncompleted"].map(filter => (
            <button 
              key={filter}
              onClick={() => setFilterType(filter)} 
              style={{ 
                padding: "10px 20px",
                borderRadius: "20px",
                border: "2px solid #2196f3",
                backgroundColor: filterType === filter ? "#2196f3" : "transparent",
                color: filterType === filter ? "#fff" : "#2196f3",
                cursor: "pointer",
                fontSize: "14px",
                fontWeight: "600",
                textTransform: "capitalize",
                transition: "all 0.3s"
              }}
            >
              {filter === "uncompleted" ? "Active" : filter}
            </button>
          ))}
        </div>

        <div style={{
          minHeight: "250px",
          maxHeight: "450px",
          overflowY: "auto",
          paddingRight: "5px"
        }}>
          {filteredTasks.length === 0 ? (
            <div style={{
              textAlign: "center",
              color: "#95a5a6",
              padding: "60px 20px",
              fontSize: "16px"
            }}>
              {filterType === "all" 
                ? "🎉 No tasks yet. Add your first task above!" 
                : filterType === "completed"
                ? "✅ No completed tasks yet."
                : "📋 No active tasks. Great job!"}
            </div>
          ) : (
            <ul style={{ listStyle: "none", padding: 0, margin: 0 }}>
              {filteredTasks.map(task => (
                <li
                  key={task.id}
                  style={{
                    display: "flex",
                    justifyContent: "space-between",
                    alignItems: "center",
                    padding: "15px",
                    marginBottom: "10px",
                    backgroundColor: task.completed ? "#e8f5e9" : "#fff",
                    borderRadius: "10px",
                    border: task.completed ? "2px solid #4caf50" : "2px solid #f5f5f5",
                    boxShadow: "0 2px 5px rgba(0,0,0,0.05)",
                    transition: "all 0.3s"
                  }}
                >
                  <span 
                    onClick={() => handleToggle(task.id)}
                    style={{
                      flex: 1,
                      textDecoration: task.completed ? "line-through" : "none",
                      color: task.completed ? "#4caf50" : "#2c3e50",
                      cursor: "pointer",
                      fontSize: "15px",
                      userSelect: "none",
                      fontWeight: task.completed ? "400" : "500"
                    }}
                  >
                    {task.completed ? "✓ " : "○ "}{task.text}
                  </span>
                  <button
                    onClick={() => handleDelete(task.id)}
                    style={{
                      backgroundColor: "#f44336",
                      color: "#fff",
                      border: "none",
                      borderRadius: "6px",
                      padding: "8px 16px",
                      cursor: "pointer",
                      fontSize: "13px",
                      fontWeight: "600",
                      marginLeft: "10px"
                    }}
                  >
                    Delete
                  </button>
                </li>
              ))}
            </ul>
          )}
        </div>

        <div style={{
          marginTop: "25px",
          paddingTop: "20px",
          borderTop: "2px solid #f0f0f0",
          textAlign: "center",
          color: "#7f8c8d",
          fontSize: "14px",
          fontWeight: "500"
        }}>
          <span style={{ marginRight: "15px" }}>
            📊 Total: {tasks.length}
          </span>
          <span style={{ marginRight: "15px" }}>
            ✅ Completed: {tasks.filter(t => t.completed).length}
          </span>
          <span>
            ⏳ Active: {tasks.filter(t => !t.completed).length}
          </span>
        </div>
      </div>
    </div>
  );
}
```
## OUTPUT
![WhatsApp Image 2025-10-15 at 2 23 53 PM](https://github.com/user-attachments/assets/76deb59d-8b20-4334-b383-468f31b0e526)
## RESULT
The Task Manager app using React where users can add, delete, toggle, and filter tasks is executed successfully.
