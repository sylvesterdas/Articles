---
title: "Installing DeepSeek-R1 on Ubuntu with Ollama"
seoTitle: "Installing DeepSeek-R1 on Ubuntu with Ollama"
seoDescription: "This tutorial provides a step-by-step guide to installing DeepSeek-R1, a powerful open-source large language model (LLM), on your local Ubuntu machine us..."
datePublished: 2025-01-29T15:02:50.302Z
cuid: cm6i1crel000209ldddat4xwq
slug: installing-deepseek-r1-on-ubuntu-with-ollama
cover: https://i.ibb.co/6714Pwqb/1e68e45be22c.png
tags: ai, cloud, programming, technology, python, devops

---

This tutorial provides a step-by-step guide to installing DeepSeek-R1, a powerful open-source large language model (LLM), on your local Ubuntu machine using Ollama, a user-friendly tool for running LLMs locally. By the end of this tutorial, you'll be able to run DeepSeek-R1 and start experimenting with its capabilities.

## Introduction

Large language models are revolutionizing how we interact with computers. DeepSeek-R1, being open-source, allows you to explore and utilize this technology without relying on cloud services. Ollama simplifies the process of running these complex models locally, making them accessible to a wider audience. This tutorial focuses on getting you set up quickly and easily.

## Prerequisites & Setup

Before we begin, ensure you have the following:

* **An Ubuntu machine:** This tutorial is specifically for Ubuntu. Other Linux distributions might require slight modifications.
    
* **Stable internet connection:** You'll need to download the necessary files.
    
* **Basic command-line knowledge:** Familiarity with using the terminal is essential.
    
* **Sufficient disk space:** LLMs can be quite large, ensure you have at least 20GB of free space.
    

## Step-by-step Implementation

1. **Install Ollama:** Open your terminal and execute the following commands:
    

```bash
curl -LO [https://github.com/jmorganca/ollama/releases/latest/download/ollama-linux-amd64](https://github.com/jmorganca/ollama/releases/latest/download/ollama-linux-amd64)
sudo install ollama-linux-amd64 /usr/local/bin/ollama
```

2. **Start the Ollama service:**
    

```bash
sudo systemctl start ollama
```

3. **Pull the DeepSeek-R1 model:**
    

```bash
ollama pull deepseek-coder-r1
```

This command downloads the DeepSeek-R1 model to your local Ollama repository. This might take some time depending on your internet speed.

4. **Run DeepSeek-R1:**
    

```bash
ollama run deepseek-coder-r1
```

This command starts an interactive session with DeepSeek-R1. You can now start typing your prompts and see the model's responses.

## Testing/Validation

After starting the model, test it with a simple prompt like:

```plaintext
What is the capital of France?
```

DeepSeek-R1 should respond with `Paris`. This confirms that the installation and setup were successful.

## Troubleshooting Common Issues

* `ollama: command not found`: This error means Ollama isn't installed correctly or isn't in your system's PATH. Double-check the installation steps and ensure Ollama is installed in `/usr/local/bin/`.
    
* **Slow download speeds:** This can be due to network congestion. Try the download again at a different time.
    
* **Out of memory errors:** If you encounter memory issues, consider using a machine with more RAM.
    

## Next Steps

Now that you have DeepSeek-R1 running locally, you can explore its various capabilities:

* **Code generation:** Ask it to generate code in different programming languages. For example: `Write a Python function to calculate the factorial of a number.`
    
* **Text summarization:** Provide it with a long text and ask for a summary.
    
* **Translation:** Experiment with translating text between different languages.
    
* **Creative writing:** Explore its creative potential by asking it to write stories, poems, or articles.
    

This installation guide provides a foundational understanding of how to run DeepSeek-R1 locally with Ollama. Remember to consult the official Ollama and DeepSeek documentation for more advanced configurations and usage examples. Happy experimenting!

---

*Follow Minifyn:*

* [Twitter](https://x.com/minifyncom)
    
* [Facebook](https://facebook.com/minifyncom)
    
* [Instagram](https://instagram.com/minifyn)
    
* [Telegram](https://t.me/minifyn)
    
* [LinkedIn](https://www.linkedin.com/company/minifyn)
    

*Try our URL shortener:* [*minifyn.com*](https://minifyn.com)