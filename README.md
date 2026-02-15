# Bhagavad Gita AI Assistant 🕉️

An intelligent AI assistant that provides wisdom and guidance from the Bhagavad Gita using advanced RAG (Retrieval-Augmented Generation) technology.

## 🌟 Features

- **Semantic Search**: Find relevant verses from 700+ slokas using semantic similarity
- **AI-Powered Guidance**: Get personalized advice based on Bhagavad Gita wisdom
- **Local LLM**: Uses Ollama (phi3.5) running in your Kubernetes cluster - no API keys needed
- **Rich Metadata**: Access Sanskrit text, transliteration, translations, and commentaries
- **Interactive Notebook**: Easy-to-use Jupyter notebook interface
- **Privacy First**: Everything runs locally on your infrastructure

## 🎯 How It Works

```
User Problem → Semantic Search → Retrieve Slokas → LLM Reasoning → Wisdom-Based Solution
```

1. User presents a problem or question
2. System searches vector database for semantically similar verses
3. Retrieved slokas are combined with the problem in a prompt
4. LLM generates compassionate, practical guidance based on Gita teachings

## 🚀 Quick Start

### 1. Create and Activate Virtual Environment (Recommended)

Create a virtual environment to isolate dependencies:

```bash
# Create virtual environment
python3 -m venv venv

# Activate virtual environment
# On Linux/Mac:
source venv/bin/activate

# On Windows:
# venv\Scripts\activate
```

Your terminal prompt should now show `(venv)` indicating the virtual environment is active.

### 2. Install Dependencies

```bash
pip install -r requirements.txt
```

### 3. Set Up Ollama Access

The application uses a local Ollama LLM running in your Kubernetes cluster. 

**If running Jupyter notebook locally** (outside the cluster), you need to port-forward:

```bash
# Port-forward Ollama service to your local machine
kubectl port-forward -n ollama svc/ollama 11434:11434
```

Keep this running in a separate terminal while using the notebook.

**If running inside the cluster** (e.g., JupyterHub), the default configuration will work automatically:
- Service URL: `http://ollama.ollama.svc.cluster.local:11434`
- Model: `phi3.5`

### 4. Configure Environment (Optional)

Copy the example configuration:

```bash
cp .env.example .env
```

Edit `.env` if you need to change defaults:
- `OLLAMA_BASE_URL` - Use `http://localhost:11434` if port-forwarding
- `OLLAMA_MODEL` - Default is `phi3.5` (already installed in your cluster)

### 5. Run the Notebook

```bash
jupyter notebook bhagavad_gita_rag.ipynb
```

### 6. Load the Data (First Time Only)

Run all cells sequentially. The first execution will:
- Fetch all 700+ verses from the Bhagavad Gita API (~5-10 minutes)
- Create embeddings using sentence-transformers
- Store everything in a local ChromaDB database

The database is persistent - you only need to do this once!

### 7. Try It Out

Use the example queries provided or ask your own questions:

```python
problem = "I'm feeling anxious about my future. How can I find peace?"
response = solve_problem_with_gita(problem, n_slokas=5)
print(response)
```

## 📚 Example Problems

The notebook includes examples for:
- Career confusion and difficult decisions
- Managing anger and emotions
- Dealing with loss and grief
- Finding work-life balance
- Overcoming fear and anxiety

## 🔧 Configuration

### Adjust Number of Retrieved Slokas

```python
response = solve_problem_with_gita(
    problem="your question here",
    n_slokas=5,  # Retrieve 5 most relevant verses (default)
)
```

### Search Within a Specific Chapter

```python
results = search_slokas(
    query="duty and dharma",
    n_results=3,
    chapter_filter=2  # Search only in Chapter 2
)
```

### Get a Specific Verse

```python
verse = get_verse_by_id(chapter=2, verse=47)
print(verse)
```

## 📖 Data Source

Verses are fetched from the comprehensive Bhagavad Gita API:
https://github.com/vedicscriptures/bhagavad-gita

This includes:
- Sanskrit original text
- Transliteration
- Multiple English translations
- Commentaries from renowned scholars (Swami Sivananda, Swami Chinmayananda, etc.)

## 🛠️ Technical Stack

- **Vector Database**: ChromaDB for semantic search
- **Embeddings**: Sentence Transformers (paraphrase-multilingual-MiniLM-L12-v2)
- **LLM**: Local Ollama (phi3.5) running in Kubernetes
- **Language**: Python 3.10+
- **Interface**: Jupyter Notebook
- **Infrastructure**: Kubernetes-native, no external API calls

## 📁 Project Structure

```
BhagavitGitaAIProject/
├── bhagavad_gita_rag.ipynb    # Main interactive notebook
├── requirements.txt            # Python dependencies
├── bg_vector_db/              # ChromaDB storage (created on first run)
└── README.md                  # This file
```

## 🎨 Advanced Features

### Database Statistics

Check the status of your vector database:

```python
print(f"Total verses: {collection.count()}")
```

### Search Quality Tuning

- **More context**: Increase `n_slokas` to 7-10 for complex questions
- **Focused answers**: Decrease to 3-4 for specific queries
- **Chapter filtering**: Use `chapter_filter` for targeted searches

## 🔮 Future Enhancements

- Web interface (Streamlit/Gradio)
- Multi-language support (Hindi, Sanskrit responses)
- Conversation memory for follow-up questions
- Audio verse recitation
- Daily verse feature
- Verse relationship visualization

## 🤝 Contributing

Contributions are welcome! Some ideas:
- Add more LLM providers
- Improve prompt engineering
- Build a web interface
- Add test cases
- Support for other scriptures

## 📝 License

See LICENSE file for details.

## 🙏 Acknowledgments

- Vedic Scriptures team for the excellent Bhagavad Gita API
- ChromaDB for the powerful vector database
- Sentence Transformers for multilingual embeddings
- Ollama for local LLM inference
- Microsoft for the phi3.5 model

## ⚡ Troubleshooting

**Problem**: Cannot connect to Ollama
- **Solution**: If running locally, ensure port-forward is active: `kubectl port-forward -n ollama svc/ollama 11434:11434`
- **Solution**: If in cluster, verify Ollama service is running: `kubectl get svc -n ollama`

**Problem**: "Model not found" error
- **Solution**: Check available models: `kubectl exec -n ollama $(kubectl get pods -n ollama -o name | head -1) -- ollama list`
- **Solution**: Current model: `phi3.5:latest`

**Problem**: Database not loading all verses
- **Solution**: Check the statistics cell to see which chapters are incomplete. Delete `bg_vector_db/` folder and reload.

**Problem**: Out of memory errors
- **Solution**: The embedding model requires ~500MB RAM. Close other applications or use a smaller model.

**Problem**: API requests timing out during data load
- **Solution**: The script has automatic retry logic. If persistent, check your internet connection to the Bhagavad Gita API.

## 📞 Support

For issues or questions:
1. Check the troubleshooting section above
2. Review the example cells in the notebook
3. Consult the Bhagavad Gita API documentation

---

🕉️ **May the wisdom of the Bhagavad Gita illuminate your path!**