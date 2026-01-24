# LLM Brief: Neo4j Import & Visualization

> Instructions for importing SEMANTIC-GRAPH.md Cypher code into Neo4j and creating visualizations

---

## Purpose

Each SEMANTIC-GRAPH.md file includes Cypher queries that can be imported into Neo4j to:

1. **Visualize relationships** — See the knowledge graph as an interactive diagram
2. **Query the data** — Explore connections between concepts
3. **Combine graphs** — Merge multiple content pieces into a larger knowledge base
4. **Screenshot for docs** — Add Neo4j visualizations back to SEMANTIC-GRAPH.md

---

## Quick Start (Free Sandbox)

### Step 1: Create Neo4j Sandbox

1. Go to [sandbox.neo4j.com](https://sandbox.neo4j.com/)
2. Sign in with Google/GitHub/email
3. Click **"Create a Sandbox"**
4. Select **"Blank Sandbox"** (not a pre-built dataset)
5. Wait for sandbox to initialize (~30 seconds)

### Step 2: Open Neo4j Browser

1. Click **"Open"** on your sandbox
2. Select **"Open with Neo4j Browser"**
3. You'll see the Cypher query editor

### Step 3: Import the Graph

1. Copy the Cypher code from a SEMANTIC-GRAPH.md file (the `### Cypher Import (Neo4j)` section)
2. Paste into the query editor
3. Click the **Play button** (or press `Ctrl+Enter`)
4. You should see "Created X nodes, Created Y relationships"

### Step 4: Visualize

Run this query to see all nodes and relationships:

```cypher
MATCH p=()-[]-()
RETURN p
```

---

## Cypher Basics

### View All Nodes

```cypher
MATCH (n)
RETURN n
```

### View All Relationships

```cypher
MATCH p=()-[]-()
RETURN p
```

### View Specific Node Type

```cypher
MATCH (n:Concept)
RETURN n
```

### View Nodes with Properties

```cypher
MATCH (n)
RETURN n.id, n.name, labels(n)
```

### Find Paths Between Nodes

```cypher
MATCH p = (a)-[*1..3]-(b)
WHERE a.id = 'node1' AND b.id = 'node2'
RETURN p
```

### Clear Database (Start Fresh)

```cypher
MATCH (n)
DETACH DELETE n
```

---

## Creating Screenshots

### Step 1: Arrange the Graph

- **Drag nodes** to position them logically
- **Group related nodes** together
- **Minimize crossing edges**
- Use the **zoom controls** to fit everything

### Step 2: Adjust Display

- Click a node to see its properties
- Double-click to expand relationships
- Use the style panel to change colors (optional)

### Step 3: Capture Screenshot

**macOS:**
- `Cmd + Shift + 4` → drag to select area

**Windows:**
- `Win + Shift + S` → drag to select area

**Browser extension:**
- Use a screenshot extension for clean captures

### Step 4: Save to Content Folder

Save the screenshot as `neo4j-view-of-semantic-graph.png` in the same folder as the SEMANTIC-GRAPH.md file.

### Step 5: Add to SEMANTIC-GRAPH.md

Add this section at the end of the file:

```markdown
---

### Neo4j Visualization

![Semantic Knowledge Graph in Neo4j](./neo4j-view-of-semantic-graph.png)

**How to import and visualize this graph in Neo4j:**

1. **Create a free Neo4j Sandbox** at [sandbox.neo4j.com](https://sandbox.neo4j.com/) — select "Blank Sandbox"
2. **Open Neo4j Browser** and paste the Cypher code above into the query editor
3. **Run the query** (click the play button or press Ctrl+Enter)
4. **Visualize the graph** with this query:
   ```cypher
   MATCH p=()-[]-()
   RETURN p
   ```
```

---

## Combining Multiple Graphs

To import multiple SEMANTIC-GRAPH.md files into one database:

1. Clear the database first (or start fresh sandbox)
2. Import each file's Cypher code sequentially
3. Add cross-references manually:

```cypher
// Link nodes from different content pieces
MATCH (a {id: 'node_from_doc1'}), (b {id: 'node_from_doc2'})
CREATE (a)-[:RELATES_TO]->(b)
```

---

## Common Issues

### "Node already exists"

The Cypher uses `CREATE` which fails on duplicates. Use `MERGE` instead:

```cypher
// Instead of CREATE
MERGE (n:Concept {id: 'concept1'})
ON CREATE SET n.name = 'Concept 1'
```

### Graph looks messy

- Drag nodes to rearrange
- Use the physics simulation toggle
- Try different layout algorithms in settings

### Can't see all nodes

- Zoom out with mouse wheel
- Use "Fit to screen" button
- Some nodes might be detached — run `MATCH (n) RETURN n` to see all

### Sandbox expired

Free sandboxes expire after 3 days. Create a new one and re-import.

---

## Neo4j Desktop (Local Alternative)

For persistent local databases:

1. Download [Neo4j Desktop](https://neo4j.com/download/)
2. Create a new project
3. Create a local DBMS
4. Open Neo4j Browser
5. Import Cypher as above

**Benefits:** No expiration, faster, works offline

---

## Advanced: Aura DB (Cloud)

For permanent cloud hosting:

1. Go to [neo4j.com/cloud/aura](https://neo4j.com/cloud/aura/)
2. Create free tier instance
3. Connect via Neo4j Browser or drivers

**Benefits:** Persistent, accessible anywhere, API access

---

## Example Workflow

```bash
# 1. Open SEMANTIC-GRAPH.md
# 2. Copy Cypher code
# 3. Go to sandbox.neo4j.com
# 4. Create blank sandbox
# 5. Paste and run Cypher
# 6. Run: MATCH p=()-[]-() RETURN p
# 7. Arrange nodes nicely
# 8. Screenshot
# 9. Save as neo4j-view-of-semantic-graph.png
# 10. Add screenshot section to SEMANTIC-GRAPH.md
```

---

## Checklist

- [ ] Neo4j sandbox created (or local/Aura)
- [ ] Cypher code copied from SEMANTIC-GRAPH.md
- [ ] Code executed successfully
- [ ] Graph visualized with `MATCH p=()-[]-() RETURN p`
- [ ] Nodes arranged logically
- [ ] Screenshot captured
- [ ] Saved as `neo4j-view-of-semantic-graph.png`
- [ ] Screenshot section added to SEMANTIC-GRAPH.md
- [ ] Import instructions included for readers
