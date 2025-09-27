<template>
  <div class="step results">
    <h2>Resultados</h2>

    <div v-if="!hasParams" class="alert">
      Parâmetros da cidade não encontrados. Selecione a localização novamente.
    </div>

    <div class="results-grid" v-else>
      <ul class="kpi-list">
        <li class="kpi-item"><span><strong>Convencional (mês)</strong></span><span>{{ toBRL(convMensal) }}</span></li>
        <li class="kpi-item"><span><strong>Convencional (12 meses)</strong></span><span>{{ toBRL(convAnual) }}</span></li>
        <li class="kpi-item"><span><strong>Renovável (mês)</strong></span><span>{{ toBRL(renMensal) }}</span></li>
        <li class="kpi-item"><span><strong>Renovável (12 meses)</strong></span><span>{{ toBRL(renAnual) }}</span></li>
        <li class="kpi-item"><span><strong>Economia (ano)</strong></span><span>{{ toBRL(economiaAnual) }} ({{ economiaPercent.toFixed(1) }}%)</span></li>
        <li class="kpi-item"><span><strong>CO₂ evitado (kg/ano)</strong></span><span>{{ co2AnoKg.toFixed(0) }}</span></li>
        <li class="kpi-item"><span><strong>Consumo estimado</strong></span><span>{{ consumoKwhMensal.toFixed(2) }} kWh/mês</span></li>
      </ul>

      <div>
        <ChartDisplay :summary="chartData" />
      </div>
    </div>

    <div class="center mt-6">
      <button class="btn btn--primary" @click="showModal = true">Deseja entrar em contato?</button>
    </div>

    <!-- Modal de Contato -->
    <div v-if="showModal" class="modal-backdrop" @click.self="showModal = false">
      <div class="modal">
        <h3>Deixe seus dados</h3>
        <form @submit.prevent="enviarContato">
          <div class="mb-2">
            <label>Nome</label>
            <input
              v-model.trim="contato.nome"
              required
              class="form-control lg"
              placeholder="Seu nome completo"
            />
          </div>

          <div class="mb-2">
            <label>E-mail</label>
            <input
              v-model="contato.email"
              @input="onEmailInput"
              type="email"
              class="form-control lg"
              placeholder="seuemail@dominio.com"
            />
            <small v-if="contato.email && !emailValido" class="text-danger">E-mail inválido.</small>
          </div>

          <div class="mb-2">
            <label>Telefone</label>
            <input
              v-model="contato.telefone"
              @input="onPhoneInput"
              class="form-control lg"
              inputmode="numeric"
              maxlength="16"
              placeholder="(00) 00000-0000"
            />
            <small v-if="contato.telefone && !foneValido" class="text-danger">Telefone incompleto.</small>
          </div>

          <div class="mb-2 form-check">
            <input type="checkbox" v-model="contato.consentimento" id="lgpd" required />
            <label for="lgpd">
              Aceito os
              <a href="#" @click.prevent="showTerms = true">Termos de Uso e Privacidade</a>.
            </label>
          </div>

          <div class="mt-3 d-flex" style="display: flex; gap: 1rem;">
            <button type="button" class="btn btn-secondary" @click="showModal=false">Cancelar</button>
            <button type="submit" class="btn btn-success" :disabled="loading">
              <span v-if="loading">Enviando...</span>
              <span v-else>Enviar</span>
            </button>
          </div>
        </form>
      </div>
    </div>

    <!-- Modal de Termos -->
    <div v-if="showTerms" class="modal-backdrop" @click.self="showTerms = false">
      <div class="modal modal-terms">
        <h3>Termos de Uso e Privacidade</h3>
        <div class="terms-body">
          <p>
            Ao prosseguir, você concorda com a coleta e o tratamento dos dados pessoais informados
            (nome, e-mail e/ou telefone) para fins de contato e acompanhamento da simulação realizada na
            Calculadora Energia Sustentável.
          </p>
          <p>
            <strong>Finalidade:</strong> uso exclusivo para retorno comercial, esclarecimento de dúvidas e envio
            de informações relacionadas à sua simulação e às opções de eficiência energética.
          </p>
          <p>
            <strong>Base legal:</strong> consentimento do titular, conforme a Lei Geral de Proteção de Dados (LGPD – Lei nº 13.709/2018).
          </p>
          <p>
            <strong>Compartilhamento:</strong> não compartilhamos seus dados com terceiros sem novo consentimento,
            exceto se exigido por lei.
          </p>
          <p>
            <strong>Segurança:</strong> adotamos medidas técnicas e administrativas para proteger suas informações.
          </p>
          <p>
            <strong>Retenção:</strong> os dados são mantidos pelo tempo necessário ao atendimento da finalidade informada.
          </p>
          <p>
            <strong>Seus direitos:</strong> acesso, correção, portabilidade, revogação do consentimento e eliminação, mediante solicitação.
          </p>
        </div>
        <div class="mt-3 text-end">
          <button class="btn btn-primary" @click="showTerms = false">Fechar</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import ChartDisplay from './ChartDisplay.vue'
import Swal from 'sweetalert2'
import 'sweetalert2/dist/sweetalert2.min.css'

export default {
  name: 'Results',
  components: { ChartDisplay },
  props: { form: { type: Object, required: true } },
  data() {
    return {
      showModal: false,
      showTerms: false,
      loading: false,
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

    eficienciaTotal() {
      const efc = this.p.eficiencias ?? []
      const seg = Number(this.form.id_segmento)
      const te  = Number(this.form.id_tipo_energia)
      const ti  = Number(this.form.id_tipo_instalacao)

      if (Array.isArray(efc)) {
        const m = efc.find(e =>
          Number(e.id_segmento) === seg &&
          Number(e.id_tipo_energia) === te &&
          Number(e.id_tipo_instalacao) === ti
        )
        const v = m ? Number(m.eficiencia_valor) : 0
        return Math.min(Math.max(v, 0), 0.95)
      }
      if (efc && typeof efc === 'object') {
        const segVal = Number(efc[seg]) || 0
        return Math.min(Math.max(segVal, 0), 0.95)
      }
      return 0
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
    },

    emailValido() {
      if (!this.contato.email) return true
      const re = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
      return re.test(this.contato.email)
    },
    foneValido() {
      if (!this.contato.telefone) return true
      const dig = this.contato.telefone.replace(/\D/g, '')
      return dig.length === 10 || dig.length === 11
    }
  },

  methods: {
    toBRL(v) {
      return (Number(v) || 0).toLocaleString('pt-BR', { style: 'currency', currency: 'BRL' })
    },

    onEmailInput() {
      this.contato.email = (this.contato.email || '').toLowerCase().replace(/\s+/g, '')
    },

    onPhoneInput() {
      const digits = (this.contato.telefone || '').replace(/\D/g, '').slice(0, 11)
      let out = digits

      if (digits.length <= 10) {
        out = digits
          .replace(/^(\d{0,2})/, '($1')
          .replace(/^\((\d{2})(\d{0,4})/, '($1) $2')
          .replace(/(\d{4})(\d{1,4})$/, '$1-$2')
      } else {
        out = digits
          .replace(/^(\d{0,2})/, '($1')
          .replace(/^\((\d{2})(\d{0,5})/, '($1) $2')
          .replace(/(\d{5})(\d{1,4})$/, '$1-$2')
      }
      this.contato.telefone = out
    },

    async enviarContato() {
      if (!this.contato.email && !this.contato.telefone) {
        return Swal.fire({
          icon: 'warning',
          title: 'Informe contato',
          text: 'Preencha ao menos e-mail ou telefone.'
        })
      }
      if (!this.emailValido) {
        return Swal.fire({ icon: 'error', title: 'E-mail inválido' })
      }
      if (!this.foneValido) {
        return Swal.fire({ icon: 'error', title: 'Telefone incompleto' })
      }
      if (!this.contato.consentimento) {
        return Swal.fire({
          icon: 'info',
          title: 'Consentimento necessário',
          text: 'É preciso aceitar os Termos de Uso e Privacidade.'
        })
      }

      const payload = {
        consentimento: this.contato.consentimento,
        id_cidade: this.form.id_cidade,
        id_tipo_energia: this.form.id_tipo_energia,
        id_tipo_instalacao: this.form.id_tipo_instalacao,
        id_segmento: this.form.id_segmento,
        id_parametro: this.form.parametros?.id_parametro ?? null,
        valor_conta_medio: this.gastoMensal,
        consumo_kwh_estimado: this.consumoKwhMensal,
        economia_reais: this.economiaAnual,
        economia_percentual: this.economiaPercent,
        co2_evitado: this.co2AnoKg,
        nome_contato: this.contato.nome,
        email_contato: this.contato.email || null,
        telefone_contato: this.contato.telefone || null
      }

      this.loading = true
      try {
        const res = await fetch('http://127.0.0.1:8000/api/simulacoes', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(payload)
        })
        if (!res.ok) {
          const detail = await res.text().catch(() => '')
          throw new Error(detail || 'Falha ao salvar')
        }
        await Swal.fire({
          icon: 'success',
          title: 'Tudo certo!',
          text: 'Simulação salva com sucesso. Em breve entraremos em contato.'
        })
        this.showModal = false
      } catch (e) {
        Swal.fire({
          icon: 'error',
          title: 'Erro ao salvar',
          text: e.message || 'Tente novamente em instantes.'
        })
      } finally {
        this.loading = false
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
  position:fixed; inset:0;
  background:rgba(0,0,0,.5);
  display:flex; align-items:center; justify-content:center;
  padding: 1rem;
}
.modal {
  background:#fff; padding:1.5rem; border-radius:8px;
  width:100%; max-width:520px;
}
.modal-terms { max-width:720px; }
.terms-body {
  max-height: 55vh; overflow:auto;
  padding-right:.5rem;
}
.form-control {
  width:94%;
  padding:.75rem .9rem;
  border:1px solid #dcdcdc; border-radius:6px;
}
.mb-2 {
  margin-bottom: .5rem;
}
.form-control.lg { font-size:1rem; }
.form-check {
  display:flex; align-items:center; gap:.5rem;
}
.btn {
  padding:.55rem 1rem; border-radius:6px; cursor:pointer; border:0;
}
.btn-primary { background:#2d89ef; color:#fff; }
.btn-success { background:#27ae60; color:#fff; }
.btn-secondary { background:#7f8c8d; color:#fff; }
.mt-3 { margin-top:1rem; }
.text-end { text-align:end; }
.text-center { text-align:center; }
.text-danger { color:#c0392b; }
</style>