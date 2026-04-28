<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>XFC</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script defer src="https://cdn.jsdelivr.net/npm/alpinejs@3.x.x/dist/cdn.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;700;900&display=swap');
        body { 
            font-family: 'Inter', sans-serif; color: white; 
            background: linear-gradient(rgba(0,0,0,0.95), rgba(0,0,0,0.98)), url('XFC.jpeg');
            background-size: cover; background-position: center; background-attachment: fixed; min-height: 100vh;
        }
        .glass { background: rgba(20, 20, 20, 0.85); backdrop-filter: blur(16px); border: 1px solid rgba(251, 191, 36, 0.2); }
        .pos-GOL { background: #ef4444; } .pos-ZAG { background: #3b82f6; }
        .pos-MEI { background: #fbbf24; color: black; } .pos-ATA { background: #f97316; }
        [x-cloak] { display: none !important; }
    </style>
</head>
<body x-data="xfcApp()" x-init="init()">

    <header class="bg-yellow-500 p-4 sticky top-0 z-50 shadow-2xl text-black">
        <div class="max-w-4xl mx-auto flex justify-between items-center">
            <button @click="resetarCiclo()" class="text-[10px] bg-black text-white px-4 py-2 rounded-lg font-black uppercase italic">🔒 Zerar</button>
            <h1 class="font-black italic text-3xl uppercase tracking-tighter">XFC</h1>
            <span class="text-[10px] font-black bg-black text-yellow-500 px-3 py-1 rounded-full" x-text="listaSorteio.length + ' JOGADORES'"></span>
        </div>
    </header>

    <main class="max-w-4xl mx-auto p-4 py-6 space-y-6">
        
        <div x-show="etapa === 'lista' && listaSorteio.length > 0" class="grid grid-cols-3 gap-2 px-2" x-transition>
            <div class="glass p-2 rounded-xl text-center border-b-2 border-red-500">
                <p class="text-[8px] uppercase text-slate-400 font-bold">Goleiros</p>
                <p class="text-lg font-black text-red-500" x-text="contagem().goleiros"></p>
            </div>
            <div class="glass p-2 rounded-xl text-center border-b-2 border-yellow-500">
                <p class="text-[8px] uppercase text-slate-400 font-bold">Craques</p>
                <p class="text-lg font-black text-yellow-500" x-text="contagem().craques"></p>
            </div>
            <div class="glass p-2 rounded-xl text-center border-b-2 border-blue-500">
                <p class="text-[8px] uppercase text-slate-400 font-bold">Visitantes</p>
                <p class="text-lg font-black text-blue-500" x-text="contagem().visitantes"></p>
            </div>
        </div>

        <div x-show="etapa === 'lista'" class="space-y-6">
            <button @click="abrirInscricao()" class="w-full bg-yellow-500 text-black py-5 rounded-2xl font-black text-xl shadow-xl uppercase active:scale-95 transition-all">⚽ ENTRAR NA LISTA</button>
            
            <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
                <template x-for="(j, i) in listaSorteio" :key="j.id">
                    <div @click="editarJogador(j)" class="glass p-4 rounded-2xl flex justify-between items-center border-l-4 border-yellow-500 relative cursor-pointer active:bg-white/10 transition-all">
                        <span class="absolute -top-1 -left-1 bg-yellow-500 text-black text-[9px] font-black w-4 h-4 flex items-center justify-center rounded-full" x-text="i + 1"></span>
                        <div class="flex items-center gap-4">
                            <div :class="'pos-' + j.posicao" class="w-10 h-10 rounded-xl flex items-center justify-center font-black text-[11px]" x-text="j.posicao"></div>
                            <div>
                                <div class="font-black uppercase text-white text-sm flex items-center gap-2">
                                    <span x-text="j.nome"></span>
                                    <span class="text-yellow-500 text-[10px]" x-text="'★'.repeat(j.nivel)"></span>
                                </div>
                                <div class="text-[9px] text-slate-500 font-bold uppercase tracking-widest" x-text="j.tipo"></div>
                            </div>
                        </div>
                        <div class="text-[8px] font-black text-zinc-700 uppercase">Ajustar</div>
                    </div>
                </template>
            </div>
            
            <button x-show="listaSorteio.length >= 4" @click="confirmarSorteio()" class="w-full bg-white text-black py-4 rounded-2xl font-black text-lg uppercase shadow-xl">🔒 Sortear Equipes</button>
        </div>

        <div x-show="etapa === 'resultado'" x-cloak class="space-y-6">
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                <template x-for="(time, i) in timesGerados">
                    <div class="glass rounded-3xl overflow-hidden">
                        <div :class="i === 0 ? 'bg-yellow-500 text-black' : 'bg-zinc-800 text-yellow-500'" 
                             class="p-3 font-black text-center uppercase text-sm" x-text="i === 0 ? '🟨 TIME AMARELO' : '⬛ TIME PRETO'"></div>
                        <div class="p-3 space-y-2">
                            <template x-for="p in time">
                                <div class="bg-black/40 p-3 rounded-xl flex justify-between items-center border border-white/5">
                                    <span class="font-bold uppercase text-xs text-slate-200" x-text="p.nome"></span>
                                    <span :class="'pos-' + p.posicao" class="text-[8px] font-black px-2 py-0.5 rounded text-white" x-text="p.posicao"></span>
                                </div>
                            </template>
                        </div>
                    </div>
                </template>
            </div>
            <div class="flex gap-2">
                <button @click="compartilharWhatsApp()" class="flex-1 bg-green-600 text-white py-4 rounded-2xl font-black uppercase text-sm">WhatsApp</button>
                <button @click="etapa = 'lista'" class="flex-1 bg-zinc-800 text-white py-4 rounded-2xl font-black uppercase text-sm">Voltar</button>
            </div>
        </div>
    </main>

    <div x-show="showModal" class="fixed inset-0 bg-black/95 z-[200] flex items-center justify-center p-4" x-cloak>
        <div class="glass p-8 rounded-[40px] w-full max-w-sm space-y-6 border-yellow-500 shadow-2xl">
            <h3 class="text-center font-black text-yellow-500 uppercase italic" x-text="editandoId ? 'Painel do Diretor' : 'Ficha XFC'"></h3>
            <div class="space-y-4">
                <div class="relative">
                    <input x-model="form.nome" @input="checkIn()" :disabled="editandoId" type="text" placeholder="SEU NOME" class="w-full bg-black p-5 rounded-2xl border border-white/10 text-white uppercase font-black outline-none focus:border-yellow-500 disabled:opacity-50">
                    <div x-show="sugestao && !editandoId" class="absolute -bottom-5 left-2 text-[9px] text-yellow-500 font-bold uppercase" x-text="'Perfil detectado: ' + sugestao.posicao"></div>
                </div>
                <div class="grid grid-cols-2 gap-3">
                    <select x-model="form.posicao" class="bg-black p-4 rounded-xl text-white text-[11px] font-bold border border-white/10 uppercase outline-none">
                        <option value="GOL">Goleiro</option><option value="ZAG">Zagueiro</option><option value="MEI">Meio</option><option value="ATA">Atacante</option>
                    </select>
                    <select x-model="form.tipo" class="bg-black p-4 rounded-xl text-white text-[11px] font-bold border border-white/10 uppercase outline-none">
                        <option value="visitante">Visitante</option><option value="mensalista">Mensalista</option><option value="mantenedor">Mantenedor</option>
                    </select>
                </div>
                <div class="text-center bg-black/40 p-4 rounded-2xl border border-white/5">
                    <p class="text-[10px] font-bold uppercase text-slate-500 mb-2">Nível Técnico</p>
                    <div class="flex justify-center gap-6">
                        <template x-for="n in [1,2,3]">
                            <button @click="form.nivel = n" :class="form.nivel >= n ? 'text-yellow-500 scale-110' : 'text-zinc-800'" class="text-3xl transition-all">★</button>
                        </template>
                    </div>
                </div>
            </div>
            <button @click="salvar()" class="w-full bg-yellow-500 text-black py-5 rounded-2xl font-black uppercase active:scale-95 transition-all" x-text="editandoId ? 'Atualizar Dados' : 'Confirmar Presença'"></button>
            <button @click="showModal = false" class="w-full text-slate-600 text-[10px] font-black uppercase">Cancelar</button>
        </div>
    </div>

    <div x-show="showRegras" class="fixed inset-0 bg-black/98 z-[300] flex items-center justify-center p-4 text-center" x-cloak x-transition>
        <div class="max-w-sm w-full space-y-6">
            <h2 class="text-2xl font-black text-yellow-500 uppercase italic">Atenção Visitante</h2>
            <div class="glass p-6 rounded-3xl space-y-4 text-left border-yellow-500/50">
                <p class="text-[12px] font-bold uppercase text-red-500">🚫 Proibido Carrinho.</p>
                <p class="text-[12px] font-bold uppercase text-blue-500">🛡️ Prioridade Mensalistas.</p>
                <p class="text-[12px] font-bold uppercase text-green-500">⏱️ Tempo: 20min (1ª) / 10min.</p>
            </div>
            <button @click="fecharRegras()" :disabled="tempoRegras > 0" :class="tempoRegras > 0 ? 'bg-zinc-800 text-zinc-500' : 'bg-yellow-500 text-black'" class="w-full py-5 rounded-2xl font-black uppercase transition-all">
                <span x-text="tempoRegras > 0 ? 'Aguarde ' + tempoRegras + 's...' : 'Entendi ✅'"></span>
            </button>
        </div>
    </div>

    <script>
        const API = "https://api.npoint.io/af23b1be1b7083d862a9"; 
        function xfcApp() {
            return {
                etapa: 'lista', showModal: false, showRegras: false, tempoRegras: 15,
                timesGerados: [], listaSorteio: [], form: { nome: '', posicao: 'MEI', tipo: 'visitante', nivel: 2 },
                senhaMestra: 'Diretoria', editandoId: null, sugestao: null,
                dbLocal: JSON.parse(localStorage.getItem('xfc_history') || '[]'),

                async init() {
                    await this.sync();
                    setInterval(() => this.sync(), 20000);
                },

                validarAcesso() {
                    const t = prompt("🔐 Senha DIRETORIA:");
                    if (t === this.senhaMestra) return true;
                    alert("❌ Acesso Negado!"); return false;
                },

                async sync() {
                    try {
                        const r = await fetch(${API}?t=${Date.now()});
                        const d = await r.json();
                        this.listaSorteio = d.sorteio || [];
                        this.timesGerados = d.times_gerados || [];
                        this.etapa = this.timesGerados.length > 0 ? 'resultado' : 'lista';
                    } catch(e) {}
                },

                async save(s, tg) {
                    await fetch(API, { method: 'POST', body: JSON.stringify({ sorteio: s, times_gerados: tg }) });
                    await this.sync();
                },

                contagem() {
                    return {
                        goleiros: this.listaSorteio.filter(j => j.posicao === 'GOL').length,
                        craques: this.listaSorteio.filter(j => j.nivel === 3).length,
                        visitantes: this.listaSorteio.filter(j => j.tipo === 'visitante').length
                    }
                },

                abrirInscricao() {
                    this.editandoId = null;
                    this.form = { nome: '', posicao: 'MEI', tipo: 'visitante', nivel: 2 };
                    this.showModal = true;
                },

                // Opção 1: Check-in Inteligente
                checkIn() {
                    if(this.editandoId) return;
                    const b = this.dbLocal.find(j => j.nome === this.form.nome.toUpperCase());
                    if(b) {
                        this.sugestao = b;
                        this.form.posicao = b.posicao;
                        this.form.nivel = b.nivel;
                        this.form.tipo = b.tipo || 'visitante';
                    } else { this.sugestao = null; }
                },

                // Opção 3: Edição Rápida
                editarJogador(j) {
                    if(!this.validarAcesso()) return;
                    this.editandoId = j.id;
                    this.form = { ...j };
                    this.showModal = true;
                },

                async salvar() {
                    const n = this.form.nome.trim().toUpperCase();
                    if(!n) return;

                    let novaLista = [...this.listaSorteio];

                    if(this.editandoId) {
                        novaLista = novaLista.map(j => j.id === this.editandoId ? { ...this.form, nome: n } : j);
                    } else {
                        if(this.listaSorteio.some(j => j.nome === n)) return alert("Já na lista!");
                        novaLista.push({ id: Date.now(), ...this.form, nome: n });
                    }

                    // Atualiza Memória Local
                    let db = this.dbLocal.filter(j => j.nome !== n);
                    db.push({ nome: n, posicao: this.form.posicao, nivel: this.form.nivel, tipo: this.form.tipo });
                    localStorage.setItem('xfc_history', JSON.stringify(db));

                    await this.save(novaLista, this.timesGerados);
                    this.showModal = false;

                    if(!this.editandoId && this.form.tipo === 'visitante') {
                        this.showRegras = true; this.tempoRegras = 15;
                        let i = setInterval(() => { if(this.tempoRegras > 0) this.tempoRegras--; else clearInterval(i); }, 1000);
                    } else { confetti({ particleCount: 100 }); }
                },

                fecharRegras() { this.showRegras = false; confetti({ particleCount: 150 }); },

                async confirmarSorteio() {
                    if (!this.validarAcesso()) return;
                    const t1 = [], t2 = [];
                    const jogs = [...this.listaSorteio].sort(() => Math.random() - 0.5);
                    jogs.forEach((j, i) => i % 2 === 0 ? t1.push(j) : t2.push(j));
                    await this.save(this.listaSorteio, [t1, t2]);
                    confetti({ particleCount: 200 });
                },

                compartilharWhatsApp() {
                    let t = ⚽ *XFC - TIMES DEFINIDOS* ⚽\n\n;
                    this.timesGerados.forEach((time, i) => {
                        t += ${i === 0 ? "🟨 *AMARELO*" : "⬛ *PRETO*"}\n;
                        time.forEach(p => t += • ${p.nome} (${p.posicao})\n);
                        t += \n;
                    });
                    window.open(https://api.whatsapp.com/send?text=${encodeURIComponent(t)}, '_blank');
                },

                async resetarCiclo() { if(this.validarAcesso()) await this.save([], []); }
            }
        }
    </script>
</body>
</html>
