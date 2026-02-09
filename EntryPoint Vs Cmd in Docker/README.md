# Docker ENTRYPOINT vs CMD ☕

## 1. The Analogy: The Coffee Maker

Imagine you are designing a **Coffee Maker** (your Docker Image).

### ENTRYPOINT: The Machine’s Primary Function

The **ENTRYPOINT** is the physical machine itself.

- If the ENTRYPOINT is `make-coffee`, this machine is built to make coffee.
- It is hard to change.
- You wouldn't buy a coffee maker and expect it to suddenly become a leaf blower.
- It defines the purpose of the container.

---

### CMD: The Default Setting

The **CMD** is the "Default Strength" setting on the machine.

- When you press "Start" without touching anything else, it uses the CMD  
  (e.g., `--strength=medium`).
- However, the user can easily override this.
- If the user wants "Extra Strong," they press a different button, and the "Medium" setting is completely ignored.

---

## 2. How They Work Together

When you run a Docker container, Docker combines them means ENTRYPOINT+CMD= THE FINAL ACTION


### Configuration Table

| Configuration | What it means | Resulting Command |
|--------------|--------------|------------------|
| ENTRYPOINT `brew` | The machine is a brewer | — |
| CMD `espresso` | If the user says nothing, make espresso | — |


### Runtime Behavior

```
docker run coffee-machine
```
-- Machine makes Espresso

```
docker run coffee-machine latte
```
-- Machine makes Latte
> CMD Espresso is deleted and replaced.


## 3. Why Is ENTRYPOINT "Mandatory"? (The Truth)
Technically, it is not mandatory. You can have a Dockerfile with only CMD.
However, engineers treat it as "mandatory" in professional settings for two main reasons.
