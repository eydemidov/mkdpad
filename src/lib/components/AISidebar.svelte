<script lang="ts">
  import { onMount } from "svelte";
  import OpenAI from "openai";

  export let getEditorContent: () => string;
  export let getSelectedText: () => string;

  let messages: Array<{ role: "user" | "assistant"; content: string }> = [];
  let inputMessage = "";
  let isLoading = false;
  let openai: OpenAI | null = null;

  onMount(() => {
    const apiKey = localStorage.getItem("openai_api_key");
    if (apiKey) {
      openai = new OpenAI({
        apiKey,
        dangerouslyAllowBrowser: true,
      });
    }
  });

  async function sendMessage() {
    if (!inputMessage.trim() || !openai) return;

    const userMessage = inputMessage.trim();
    inputMessage = "";
    messages = [...messages, { role: "user", content: userMessage }];
    isLoading = true;

    try {
      const editorContent = getEditorContent();
      const selectedText = getSelectedText();

      let contextMessage = "";
      if (selectedText) {
        contextMessage = `\n\nSelected text from editor:\n"${selectedText}"`;
      } else if (editorContent) {
        contextMessage = `\n\nCurrent document content:\n"${editorContent}"`;
      }

      const response = await openai.chat.completions.create({
        model: "gpt-4o-mini",
        messages: [
          {
            role: "system",
            content:
              "You are a helpful AI writing assistant for a markdown text editor. Help users with writing, editing, and formatting their markdown content. When provided with document context, analyze it and provide relevant suggestions.",
          },
          ...messages.map((msg) => ({ role: msg.role, content: msg.content })),
          {
            role: "user",
            content: userMessage + contextMessage,
          },
        ],
        max_tokens: 800,
      });

      const assistantMessage =
        response.choices[0]?.message?.content ||
        "I could not generate a response.";
      messages = [
        ...messages,
        { role: "assistant", content: assistantMessage },
      ];
    } catch (error) {
      messages = [
        ...messages,
        {
          role: "assistant",
          content: "there was an error processing your request.",
        },
      ];
    } finally {
      isLoading = false;
    }
  }

  function setApiKey() {
    const apiKey = prompt("Enter your OpenAI API key:");
    if (apiKey) {
      localStorage.setItem("openai_api_key", apiKey);
      openai = new OpenAI({
        apiKey,
        dangerouslyAllowBrowser: true,
      });
    }
  }

  function handleKeydown(event: KeyboardEvent) {
    if (event.key === "Enter" && !event.shiftKey) {
      event.preventDefault();
      sendMessage();
    }
  }

  function analyzeDocument() {
    const content = getEditorContent();
    if (content && content.trim()) {
      const prompt = `Analyze this markdown document and provide suggestions for improvement:\n\n${content}`;
      messages = [...messages, { role: "user", content: prompt }];
      isLoading = true;

      openai?.chat.completions
        .create({
          model: "gpt-4o-mini",
          messages: [
            {
              role: "system",
              content:
                "You are a helpful AI writing assistant for a markdown text editor. Help users with writing, editing, and formatting their markdown content. When provided with document context, analyze it and provide relevant suggestions.",
            },
            ...messages.map((msg) => ({
              role: msg.role,
              content: msg.content,
            })),
            {
              role: "user",
              content: prompt,
            },
          ],
          max_tokens: 800,
        })
        .then((response) => {
          const assistantMessage =
            response.choices[0]?.message?.content ||
            "I could not generate a response.";
          messages = [
            ...messages,
            { role: "assistant", content: assistantMessage },
          ];
          isLoading = false;
        })
        .catch((error) => {
          messages = [
            ...messages,
            {
              role: "assistant",
              content: "there was an error processing your request.",
            },
          ];
          isLoading = false;
        });
    } else {
      messages = [
        ...messages,
        {
          role: "assistant",
          content:
            "Write some content in the editor first, then click 'Analyze Document'.",
        },
      ];
    }
  }

  function improveSelectedText() {
    const selectedText = getSelectedText();

    if (selectedText && selectedText.trim()) {
      const prompt = `Improve this text:\n\n${selectedText}`;
      messages = [...messages, { role: "user", content: prompt }];
      isLoading = true;

      openai?.chat.completions
        .create({
          model: "gpt-4o-mini",
          messages: [
            {
              role: "system",
              content:
                "You are a helpful AI writing assistant for a markdown text editor. Help users with writing, editing, and formatting their markdown content. When provided with document context, analyze it and provide relevant suggestions.",
            },
            ...messages.map((msg) => ({
              role: msg.role,
              content: msg.content,
            })),
            {
              role: "user",
              content: prompt,
            },
          ],
          max_tokens: 800,
        })
        .then((response) => {
          const assistantMessage =
            response.choices[0]?.message?.content ||
            "I could not generate a response.";
          messages = [
            ...messages,
            { role: "assistant", content: assistantMessage },
          ];
          isLoading = false;
        })
        .catch((error) => {
          messages = [
            ...messages,
            {
              role: "assistant",
              content: " there was an error processing your request.",
            },
          ];
          isLoading = false;
        });
    } else {
      messages = [
        ...messages,
        {
          role: "assistant",
          content:
            "Select some text in the editor first, then click 'Improve Selection'.",
        },
      ];
    }
  }

  function formatDocument() {
    const content = getEditorContent();
    if (content && content.trim()) {
      const prompt = `Format this markdown document properly:\n\n${content}`;
      messages = [...messages, { role: "user", content: prompt }];
      isLoading = true;

      openai?.chat.completions
        .create({
          model: "gpt-4o-mini",
          messages: [
            {
              role: "system",
              content:
                "You are a helpful AI writing assistant for a markdown text editor. Help users with writing, editing, and formatting their markdown content. When provided with document context, analyze it and provide relevant suggestions.",
            },
            ...messages.map((msg) => ({
              role: msg.role,
              content: msg.content,
            })),
            {
              role: "user",
              content: prompt,
            },
          ],
          max_tokens: 800,
        })
        .then((response) => {
          const assistantMessage =
            response.choices[0]?.message?.content ||
            "I could not generate a response.";
          messages = [
            ...messages,
            { role: "assistant", content: assistantMessage },
          ];
          isLoading = false;
        })
        .catch((error) => {
          messages = [
            ...messages,
            {
              role: "assistant",
              content: "there was an error processing your request.",
            },
          ];
          isLoading = false;
        });
    } else {
      messages = [
        ...messages,
        {
          role: "assistant",
          content:
            "Write some content in the editor first, then click 'Format Document'.",
        },
      ];
    }
  }
</script>

<div class="h-full flex flex-col bg-neutral-800">
  <div class="p-3 border-b border-neutral-700">
    <h2 class="text-sm font-medium text-neutral-300">Writing Assistant</h2>
    {#if !openai}
      <button
        on:click={setApiKey}
        class="mt-2 w-full px-2 py-1 text-xs bg-neutral-600 text-white rounded hover:bg-neutral-700 transition-colors duration-200"
      >
        Set OpenAI API Key
      </button>
    {:else}
      <div class="mt-3 space-y-2">
        <button
          on:click={analyzeDocument}
          class="w-full px-2 py-1 text-xs bg-neutral-600 text-white rounded hover:bg-neutral-700 transition-colors duration-200"
        >
          Analyze Document
        </button>
        <button
          on:click={improveSelectedText}
          class="w-full px-2 py-1 text-xs bg-neutral-600 text-white rounded hover:bg-neutral-700 transition-colors duration-200"
        >
          Improve Selection
        </button>
        <button
          on:click={formatDocument}
          class="w-full px-2 py-1 text-xs bg-neutral-600 text-white rounded hover:bg-neutral-700 transition-colors duration-200"
        >
          Format Document
        </button>
      </div>
    {/if}
  </div>

  <div class="flex-1 overflow-y-auto p-3 space-y-3">
    {#each messages as message}
      <div
        class="flex {message.role === 'user' ? 'justify-end' : 'justify-start'}"
      >
        <div
          class="max-w-xs px-3 py-2 rounded text-xs {message.role === 'user'
            ? 'bg-neutral-600 text-white'
            : 'bg-neutral-700 border border-neutral-600 text-neutral-200'}"
        >
          <p class="whitespace-pre-wrap leading-relaxed">{message.content}</p>
        </div>
      </div>
    {/each}

    {#if isLoading}
      <div class="flex justify-start">
        <div
          class="max-w-xs px-3 py-2 rounded bg-neutral-700 border border-neutral-600"
        >
          <div class="flex space-x-1">
            <div
              class="w-1.5 h-1.5 bg-neutral-400 rounded-full animate-bounce"
            ></div>
            <div
              class="w-1.5 h-1.5 bg-neutral-400 rounded-full animate-bounce"
              style="animation-delay: 0.1s"
            ></div>
            <div
              class="w-1.5 h-1.5 bg-neutral-400 rounded-full animate-bounce"
              style="animation-delay: 0.2s"
            ></div>
          </div>
        </div>
      </div>
    {/if}
  </div>

  <div class="p-3 border-t border-neutral-700">
    <div class="flex space-x-2">
      <textarea
        bind:value={inputMessage}
        on:keydown={handleKeydown}
        placeholder="Ask AI for help..."
        class="flex-1 px-2 py-1 text-xs bg-neutral-700 border border-neutral-600 rounded resize-none focus:outline-none focus:ring-1 focus:ring-neutral-500 focus:border-transparent text-neutral-200 placeholder-neutral-400"
        rows="2"
        disabled={!openai || isLoading}
      ></textarea>
      <button
        on:click={sendMessage}
        disabled={!inputMessage.trim() || !openai || isLoading}
        class="px-2 py-1 text-xs bg-neutral-600 text-white rounded hover:bg-neutral-700 disabled:opacity-50 disabled:cursor-not-allowed transition-colors duration-200"
      >
        Send
      </button>
    </div>
  </div>
</div>
