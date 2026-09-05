<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GAJA — AI Agent</title>
    <link rel="icon" href="data:image/svg+xml,<svg xmlns=%22http://www.w3.org/2000/svg%22 viewBox=%220 0 100 100%22><text y=%22.9em%22 font-size=%2290%22>🤖</text></svg>">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/styles/atom-one-dark.min.css">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/highlight.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=JetBrains+Mono:wght@400;500&display=swap');
        
        body {
            font-family: 'Inter', sans-serif;
            background: linear-gradient(135deg, #0a0a0f 0%, #12121a 50%, #1a1a2e 100%);
            background-attachment: fixed;
        }
        
        .glass {
            background: rgba(255, 255, 255, 0.03);
            backdrop-filter: blur(16px);
            -webkit-backdrop-filter: blur(16px);
            border: 1px solid rgba(255, 255, 255, 0.06);
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
        }
        
        .message-bubble { 
            animation: slideIn 0.4s cubic-bezier(0.16, 1, 0.3, 1); 
        }
        
        @keyframes slideIn {
            from { opacity: 0; transform: translateY(16px) scale(0.95); }
            to { opacity: 1; transform: translateY(0) scale(1); }
        }
        
        .typing-dot { 
            animation: bounce 1.4s infinite ease-in-out both; 
            width: 6px; height: 6px;
        }
        .typing-dot:nth-child(1) { animation-delay: -0.32s; }
        .typing-dot:nth-child(2) { animation-delay: -0.16s; }
        
        @keyframes bounce {
            0%, 80%, 100% { transform: scale(0.6); opacity: 0.4; }
            40% { transform: scale(1); opacity: 1; }
        }
        
        .tool-call { 
            border-left: 3px solid #f59e0b; 
            background: rgba(245, 158, 11, 0.06);
        }
        .tool-result { 
            border-left: 3px solid #10b981; 
            background: rgba(16, 185, 129, 0.06);
        }
        
        .gaja-glow {
            box-shadow: 0 0 20px rgba(139, 92, 246, 0.15), 0 0 40px rgba(236, 72, 153, 0.1);
        }
        
        .gaja-text {
            background: linear-gradient(135deg, #a78bfa 0%, #f472b6 50%, #fb923c 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }
        
        ::-webkit-scrollbar { width: 5px; }
        ::-webkit-scrollbar-track { background: transparent; }
        ::-webkit-scrollbar-thumb { background: rgba(255,255,255,0.1); border-radius: 10px; }
        ::-webkit-scrollbar-thumb:hover { background: rgba(255,255,255,0.2); }
        
        .prose pre { margin: 0.6em 0; border-radius: 0.6rem; }
        .prose code { color: #c4b5fd; font-family: 'JetBrains Mono', monospace; }
        .prose p { margin: 0.5em 0; line-height: 1.7; }
        .prose ul { margin: 0.5em 0; padding-left: 1.2em; }
        .prose li { margin: 0.2em 0; }
        .prose h1, .prose h2, .prose h3 { margin: 0.8em 0 0.4em; font-weight: 600; }
        
        .input-glow:focus {
            box-shadow: 0 0 0 2px rgba(139, 92, 246, 0.2), 0 0 20px rgba(139, 92, 246, 0.1);
        }
        
        .btn-send {
            background: linear-gradient(135deg, #7c3aed 0%, #db2777 100%);
            transition: all 0.2s ease;
        }
        .btn-send:hover {
            transform: translateY(-1px);
            box-shadow: 0 4px 20px rgba(124, 58, 237, 0.4);
        }
        .btn-send:active { transform: translateY(0) scale(0.98); }
        .btn-send:disabled { opacity: 0.4; cursor: not-allowed; transform: none; }
    </style>
</head>
<body class="text-white h-screen overflow-hidden selection:bg-violet-500/30">
    <div class="max-w-3xl mx-auto h-full flex flex-col p-3 md:p-5">
        
        <!-- Header -->
        <header class="glass rounded-2xl p-4 md:p-5 mb-3 flex items-center justify-between shrink-0 gaja-glow">
            <div class="flex items-center gap-3">
                <div class="relative group">
                    <div class="w-11 h-11 rounded-xl bg-gradient-to-br from-violet-500 via-fuchsia-500 to-rose-500 flex items-center justify-center text-xl shadow-lg shadow-violet-500/20 group-hover:shadow-violet-500/40 transition-all duration-500">
                        🤖
                    </div>
                    <div class="absolute -bottom-0.5 -right-0.5 w-3.5 h-3.5 bg-emerald-400 border-2 border-[#12121a] rounded-full animate-pulse"></div>
                </div>
                <div>
                    <h1 class="text-lg font-bold gaja-text tracking-tight">GAJA</h1>
                    <p class="text-[10px] text-gray-500 tracking-widest uppercase font-medium">AI Agent • Llama 3.3 70B • Tool Enabled</p>
                </div>
            </div>
            <div class="flex items-center gap-2">
                <div class="hidden md:flex items-center gap-1.5 px-3 py-1.5 rounded-lg bg-white/[0.04] border border-white/[0.06] text-[10px] text-gray-500">
                    <span class="w-1.5 h-1.5 rounded-full bg-emerald-500"></span>
                    Online
                </div>
                <button onclick="newChat()" class="group flex items-center gap-2 px-3.5 py-2 rounded-xl bg-white/[0.04] hover:bg-white/[0.08] border border-white/[0.08] hover:border-white/[0.15] transition-all text-sm font-medium text-gray-300 hover:text-white">
                    <svg class="w-4 h-4 transition-transform group-hover:rotate-90" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"></path>
                    </svg>
                    New Chat
                </button>
            </div>
        </header>

        <!-- Chat Area -->
        <div id="chat-box" class="flex-1 glass rounded-2xl p-4 md:p-6 overflow-y-auto mb-3 space-y-5 min-h-0">
            <div class="flex flex-col items-center justify-center h-full text-center">
                <div class="w-20 h-20 rounded-2xl bg-gradient-to-br from-violet-500/10 to-fuchsia-500/10 flex items-center justify-center mb-5 text-4xl border border-white/[0.05]">
                    ✨
                </div>
                <h2 class="text-2xl font-bold mb-2 text-gray-100">Meet GAJA</h2>
                <p class="text-sm text-gray-500 max-w-sm leading-relaxed">
                    Your personal AI agent with memory, web search, and real-time calculations. 
                    Powered by Llama 3.3 70B.
                </p>
                <div class="flex flex-wrap justify-center gap-2 mt-7">
                    <span class="px-3 py-1.5 rounded-lg bg-white/[0.03] border border-white/[0.06] text-[11px] text-gray-400 font-mono flex items-center gap-1.5">
                        <span class="text-amber-400">🔍</span> Web Search
                    </span>
                    <span class="px-3 py-1.5 rounded-lg bg-white/[0.03] border border-white/[0.06] text-[11px] text-gray-400 font-mono flex items-center gap-1.5">
                        <span class="text-blue-400">🧮</span> Calculator
                    </span>
                    <span class="px-3 py-1.5 rounded-lg bg-white/[0.03] border border-white/[0.06] text-[11px] text-gray-400 font-mono flex items-center gap-1.5">
                        <span class="text-emerald-400">🧠</span> Memory
                    </span>
                    <span class="px-3 py-1.5 rounded-lg bg-white/[0.03] border border-white/[0.06] text-[11px] text-gray-400 font-mono flex items-center gap-1.5">
                        <span class="text-violet-400">⚡</span> Streaming
                    </span>
                </div>
            </div>
        </div>

        <!-- Input -->
        <div class="glass rounded-2xl p-3 shrink-0">
            <div class="flex gap-2 items-end">
                <div class="flex-1 relative">
                    <textarea 
                        id="input" 
                        rows="1"
                        placeholder="Ask GAJA anything..." 
                        class="w-full bg-black/20 border border-white/[0.08] rounded-xl px-4 py-3 pr-10 focus:outline-none focus:border-violet-500/40 input-glow transition-all duration-300 resize-none text-white placeholder-gray-600 text-[15px] max-h-32"
                        oninput="this.style.height='auto';this.style.height=this.scrollHeight+'px'"
                        onkeydown="if(event.key==='Enter' && !event.shiftKey){event.preventDefault();send()}"
                    ></textarea>
                </div>
                <button 
                    id="send-btn"
                    onclick="send()" 
                    class="btn-send text-white p-3 rounded-xl disabled:opacity-40"
                >
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 19l9 2-9-18-9 18 9-2zm0 0v-8"></path>
                    </svg>
                </button>
            </div>
            <div class="mt-2 text-[10px] text-gray-600 text-center">
                GAJA may make mistakes. Verify important information.
            </div>
        </div>
    </div>

    <script>
        // ═══════════════════════════════════════════════════════════
        // CONFIGURE YOUR HF SPACE URL HERE
        // ═══════════════════════════════════════════════════════════
        const API_URL = "https://huggingface.co/spaces/Gajuiitg/gaja-agent";
        // Example: "https://gajuiitg-gaja-agent.hf.space/chat"https://huggingface.co/spaces/Gajuiitg/gaja-agent
        // ═══════════════════════════════════════════════════════════
        
        let threadId = "gaja_" + Math.random().toString(36).substr(2, 9);
        const chatBox = document.getElementById('chat-box');
        const input = document.getElementById('input');
        const sendBtn = document.getElementById('send-btn');

        function appendUser(text) {
            const div = document.createElement('div');
            div.className = 'message-bubble flex justify-end';
            div.innerHTML = `
                <div class="max-w-[85%] bg-gradient-to-br from-violet-600/90 to-fuchsia-600/90 rounded-2xl rounded-tr-sm px-5 py-3 text-white shadow-lg shadow-violet-500/10 text-[15px] leading-relaxed border border-white/[0.08]">
                    ${escapeHtml(text).replace(/\n/g, '<br>')}
                </div>
            `;
            chatBox.appendChild(div);
            scroll();
            return div;
        }

        function appendBot() {
            const div = document.createElement('div');
            div.className = 'message-bubble flex justify-start';
            div.innerHTML = `
                <div class="max-w-[90%] w-full space-y-2">
                    <div class="bg-white/[0.04] border border-white/[0.06] rounded-2xl rounded-tl-sm px-5 py-4 text-gray-100 shadow-sm">
                        <div class="prose prose-invert max-w-none text-[15px] leading-relaxed"></div>
                    </div>
                    <div class="tool-area space-y-1.5"></div>
                </div>
            `;
            chatBox.appendChild(div);
            scroll();
            return {
                container: div,
                prose: div.querySelector('.prose'),
                tools: div.querySelector('.tool-area')
            };
        }

        function appendTool(type, content) {
            const isCall = type === 'tool_call';
            const div = document.createElement('div');
            div.className = `text-xs font-mono p-3 rounded-r-lg rounded-bl-lg ${isCall ? 'tool-call text-amber-200/90' : 'tool-result text-emerald-200/90'}`;
            div.innerHTML = `
                <div class="flex items-center gap-2 mb-1.5 opacity-60">
                    <span class="text-sm">${isCall ? '🔧' : '📊'}</span>
                    <span class="uppercase tracking-wider text-[10px] font-bold">${isCall ? 'GAJA is using tool' : 'Tool result received'}</span>
                </div>
                <div class="whitespace-pre-wrap break-words leading-relaxed">${escapeHtml(content)}</div>
            `;
            return div;
        }

        function showTyping() {
            const id = 'typing-' + Date.now();
            const div = document.createElement('div');
            div.id = id;
            div.className = 'message-bubble flex justify-start';
            div.innerHTML = `
                <div class="bg-white/[0.04] rounded-2xl rounded-tl-sm px-5 py-3.5 flex gap-2 items-center h-11 border border-white/[0.06]">
                    <div class="typing-dot bg-violet-400 rounded-full"></div>
                    <div class="typing-dot bg-fuchsia-400 rounded-full"></div>
                    <div class="typing-dot bg-rose-400 rounded-full"></div>
                </div>
            `;
            chatBox.appendChild(div);
            scroll();
            return id;
        }

        function removeTyping(id) {
            const el = document.getElementById(id);
            if (el) el.remove();
        }

        function escapeHtml(text) {
            const div = document.createElement('div');
            div.textContent = text;
            return div.innerHTML;
        }

        function scroll() {
            chatBox.scrollTo({ top: chatBox.scrollHeight, behavior: 'smooth' });
        }

        async function send() {
            const text = input.value.trim();
            if (!text || sendBtn.disabled) return;
            
            input.value = '';
            input.style.height = 'auto';
            appendUser(text);
            
            const typingId = showTyping();
            sendBtn.disabled = true;
            
            let bot = null;
            let buffer = '';

            try {
                const res = await fetch(API_URL, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ message: text, thread_id: threadId })
                });

                const reader = res.body.getReader();
                const decoder = new TextDecoder();

                while (true) {
                    const { done, value } = await reader.read();
                    if (done) break;

                    const lines = decoder.decode(value).split('\n');
                    for (const line of lines) {
                        if (!line.startsWith('data: ')) continue;
                        
                        try {
                            const data = JSON.parse(line.slice(6));
                            
                            if (data.type === 'done') {
                                removeTyping(typingId);
                            }
                            else if (data.type === 'tool_call') {
                                removeTyping(typingId);
                                if (!bot) bot = appendBot();
                                bot.tools.appendChild(appendTool('tool_call', data.content));
                                scroll();
                            }
                            else if (data.type === 'tool_result') {
                                if (!bot) bot = appendBot();
                                bot.tools.appendChild(appendTool('tool_result', data.content));
                                scroll();
                            }
                            else if (data.type === 'token') {
                                removeTyping(typingId);
                                if (!bot) bot = appendBot();
                                buffer += data.content;
                                bot.prose.innerHTML = marked.parse(buffer);
                                bot.prose.querySelectorAll('pre code').forEach(hljs.highlightElement);
                                scroll();
                            }
                        } catch (e) { console.warn('Parse error', e); }
                    }
                }
            } catch (err) {
                removeTyping(typingId);
                const errorDiv = document.createElement('div');
                errorDiv.className = 'message-bubble flex justify-start';
                errorDiv.innerHTML = `
                    <div class="bg-red-500/8 border border-red-500/15 text-red-300/80 rounded-2xl rounded-tl-sm px-5 py-3 text-sm">
                        <span class="font-bold">Connection Error:</span> Could not reach GAJA's brain. Make sure your Hugging Face Space is running and the API_URL is correct.
                    </div>
                `;
                chatBox.appendChild(errorDiv);
                scroll();
            } finally {
                sendBtn.disabled = false;
                input.focus();
            }
        }

        function newChat() {
            threadId = "gaja_" + Math.random().toString(36).substr(2, 9);
            chatBox.innerHTML = `
                <div class="flex flex-col items-center justify-center h-full text-center">
                    <div class="w-20 h-20 rounded-2xl bg-gradient-to-br from-violet-500/10 to-fuchsia-500/10 flex items-center justify-center mb-5 text-4xl border border-white/[0.05]">
                        🔄
                    </div>
                    <h2 class="text-2xl font-bold mb-2 text-gray-100">New Conversation</h2>
                    <p class="text-sm text-gray-500">Memory reset. GAJA is ready for something new.</p>
                </div>
            `;
        }
    </script>
</body>
</html>
