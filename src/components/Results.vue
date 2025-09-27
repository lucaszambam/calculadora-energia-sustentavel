<template>
  <div class="step results">
    <h2>Resultados</h2>

    <div v-if="!hasParams" class="alert">
      Parâmetros da cidade não encontrados. Selecione a localização novamente.
    </div>

    <ul class="kpis" v-else>
      <li><strong>Convencional (mês):</strong> {{ toBRL(convMensal) }}</li>
      <li><strong>Convencional (12 meses):</strong> {{ toBRL(convAnual) }}</li>
      <li><strong>Renovável (mês):</strong> {{ toBRL(renMensal) }}</li>
      <li><strong>Renovável (12 meses):</strong> {{ toBRL(renAnual) }}</li>
      <li><strong>Economia (ano):</strong> {{ toBRL(economiaAnual) }} ({{ economiaPercent.toFixed(1) }}%)</li>
      <li><strong>CO₂ evitado (kg/ano):</strong> {{ co2AnoKg.toFixed(0) }}</li>
      <li><strong>Consumo estimado:</strong> {{ consumoKwhMensal.toFixed(2) }} kWh/mês</li>
    </ul>

    <ChartDisplay
      v-if="hasParams"
      :summary="chartData"
    />
    <!-- Botão de contato -->
    <div class="text-center mt-3">
      <button class="btn btn-primary" @click="showModal = true">Deseja entrar em contato?</button>
    </div>

    <!-- Modal -->
    <div v-if="showModal" class="modal-backdrop">
      <div class="modal">
        <h3>Deixe seus dados</h3>
        <form @submit.prevent="enviarContato">
          <div class="mb-2">
            <label>Nome</label>
            <input v-model="contato.nome" required class="form-control"/>
          </div>
          <div class="mb-2">
            <label>Email</label>
            <input v-model="contato.email" type="email" class="form-control"/>
          </div>
          <div class="mb-2">
            <label>Telefone</label>
            <input v-model="contato.telefone" class="form-control"/>
          </div>
          <div class="mb-2 form-check">
            <input type="checkbox" v-model="contato.consentimento" id="lgpd" required/>
            <label for="lgpd">Aceito o uso dos meus dados conforme a LGPD</label>
          </div>
          <div class="mt-3 d-flex justify-content-between">
            <button type="button" class="btn btn-secondary" @click="showModal=false">Cancelar</button>
            <button type="submit" class="btn btn-success">Enviar</button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import ChartDisplay from './ChartDisplay.vue'

export default {
  name: 'Results',
  components: { ChartDisplay },
  props: { form: { type: Object, required: true } },
  data() {
    return {
      showModal: false,
      contato: { nome: '', email: '', telefone: '', consentimento: false }
    }
  },
  computed: {
    chartData() {
      return {
        convMensal: this.convMensal,
        renMensal: this.renMensal,
        convAnual: this.convAnual,
        renAnual: this.renAnual,
        economiaAnual: this.economiaAnual,
        co2AnoKg: this.co2AnoKg
      }
    },
    hasParams() {
      const p = this.form.parametros
      return !!(p && (p.tarifa_base ?? null) !== null)
    },

    gastoMensal() {
      return Number(this.form.gastoMensal ?? this.form.expense ?? 0) || 0
    },
    p() { return this.form.parametros || {} },

    tarifaBase() { return Number(this.p.tarifa_base ?? 0) || 0 },
    taxaDistribuicao() { return Number(this.p.taxa_distribuicao ?? 0) || 0 },
    co2PorKwh() { return Number(this.p.co2_por_kwh ?? 0.084) || 0.084 },

    tarifaEfetiva() {
      if (this.tarifaBase <= 0) return 0
      return this.tarifaBase * (1 + this.taxaDistribuicao)
    },
    consumoKwhMensal() {
      if (!this.hasParams || this.tarifaEfetiva <= 0) return 0
      return this.gastoMensal / this.tarifaEfetiva
    },

    eficienciaSegmento() {
      const segId = Number(this.form.id_segmento ?? 0)
      const ef = this.p.eficiencias || {}
      return Number(ef[segId] ?? 0) || 0
    },
    ajusteInstalacao() {
      const id = Number(this.form.id_tipo_instalacao ?? 1)
      const map = { 1: 0.00, 2: -0.02, 3: -0.04 }
      return Number(map[id] ?? 0)
    },
    eficienciaTotal() {
      const efc = this.p.eficiencias ?? [];
      // Se o back filtrou, já virá só com a combinação certa; de qualquer forma, filtramos de novo:
      if (Array.isArray(efc)) {
        const seg = Number(this.form.id_segmento);
        const te  = Number(this.form.id_tipo_energia);
        const ti  = Number(this.form.id_tipo_instalacao);
        const m = efc.find(e =>
          Number(e.id_segmento) === seg &&
          Number(e.id_tipo_energia) === te &&
          Number(e.id_tipo_instalacao) === ti
        );
        const v = m ? Number(m.eficiencia_valor) : 0;
        return Math.min(Math.max(v, 0), 0.95);
      }
      // fallback nacional/estadual (objeto por segmento)
      if (efc && typeof efc === 'object') {
        const segVal = Number(efc[this.form.id_segmento]) || 0;
        return Math.min(Math.max(segVal, 0), 0.95);
      }
      return 0;
    },
    convMensal() { return this.gastoMensal },
    convAnual() { return this.convMensal * 12 },

    renMensal() {
      if (!this.hasParams) return this.convMensal
      return this.convMensal * (1 - this.eficienciaTotal)
    },
    renAnual() { return this.renMensal * 12 },

    economiaAnual() { return Math.max(this.convAnual - this.renAnual, 0) },
    economiaPercent() {
      if (this.convAnual <= 0) return 0
      return (this.economiaAnual / this.convAnual) * 100
    },

    co2AnoKg() {
      if (!this.hasParams) return 0
      const compensadoMes = this.consumoKwhMensal * this.eficienciaTotal
      return (compensadoMes * 12) * this.co2PorKwh
    }
  },

  methods: {
    toBRL(v) {
      return (Number(v) || 0).toLocaleString('pt-BR', { style: 'currency', currency: 'BRL' })
    },
    async enviarContato() {
      if (!this.contato.email && !this.contato.telefone) {
        alert('Informe pelo menos um email ou telefone')
        return
      }
      const payload = {
        consentimento: this.contato.consentimento,
        id_cidade: this.form.id_cidade,
        id_tipo_energia: this.form.id_tipo_energia,
        id_tipo_instalacao: this.form.id_tipo_instalacao,
        id_segmento: this.form.id_segmento,
        id_parametro: this.form.parametros?.id_parametro,
        valor_conta_medio: this.gastoMensal,
        consumo_kwh_estimado: this.consumoKwhMensal,
        economia_reais: this.economiaAnual,
        economia_percentual: this.economiaPercent,
        co2_evitado: this.co2AnoKg,
        nome_contato: this.contato.nome,
        email_contato: this.contato.email || null,
        telefone_contato: this.contato.telefone || null
      }
      try {
        const res = await fetch('http://127.0.0.1:8000/api/simulacoes', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(payload)
        });
        if (!res.ok) throw new Error('Falha ao salvar');
        alert('Simulação salva com sucesso! Em breve entraremos em contato.');
        this.showModal = false;
      } catch (e) {
        alert('Erro: ' + e.message);
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.results { background:#ecf0f1; padding:1rem; border-radius:6px; }
.kpis { list-style:none; padding:0; margin:0 0 1rem 0; }
.kpis li { margin:.35rem 0; }
.alert { background:#fff3cd; border:1px solid #ffeeba; padding:.75rem; border-radius:4px; margin-bottom:1rem; }

.modal-backdrop {
  position:fixed; top:0; left:0; width:100%; height:100%;
  background:rgba(0,0,0,.5); display:flex; align-items:center; justify-content:center;
}
.modal {
  background:#fff; padding:1.5rem; border-radius:6px; width:100%; max-width:400px;
}
</style>