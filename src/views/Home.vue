<template>
  <div class="home pt-5">
    <div class="row" id="selecionarTempo" v-show="contadorStatus == 'parado'">
      <div class="col-md-12">
          <label for="fileMusic" class="btn btn-outline-success">
            <span v-if="sound == ''">Selecione o som do alarme</span>
            <span v-else>{{sound}}</span>
          </label>
      </div>
      <div class="col-md-12" v-show="sound != ''">
          <button class="btn btn-outline-success" v-show="soundState == 'play'" @click="stopAudio()">
                <i class="fas fa-stop"></i>
                Parar Musica
            </button>
            <button class="btn btn-outline-success" v-show="soundState == 'pause'" @click="playAudio()">
                <i class="fas fa-play"></i>
                Tocar Musica
            </button>
      </div>
      <audio id="myAudio" style="display:none;">
      </audio>
      <input type="file" id="fileMusic" @change="musicChange" v-show="false">
      <div class="col-12 pt-3">
        <h5 class="text-success">Selecione o Tempo de Produção</h5>
      </div>
      <div class="col">
        <label for="" class="text-success"> Horas: </label>
        <select class="custom-select" id="inlineFormCustomSelect" v-model="horasProducao">
            <option value="00" selected>00</option>
            <option v-for="hora in listaHoras" :key="hora"  :value="hora">{{hora}}</option>
        </select>
      </div>
      <div class="col">
        <label for="" class="text-success"> Minutos: </label>
        <select class="custom-select" id="inlineFormCustomSelect" v-model="minutosProducao">
          <option value="00" selected>00</option>
          <option v-for="minuto in lista_Min_Seg" :key="minuto"  :value="minuto">{{minuto}}</option>
        </select>
      </div>
      <div class="col">
        <label for="" class="text-success"> Segundos: </label>
        <select class="custom-select" id="inlineFormCustomSelect" v-model="segundosProducao">
          <option value="00" selected>00</option>
          <option v-for="segundo in lista_Min_Seg" :key="segundo"  :value="segundo">{{segundo}}</option>
        </select>
      </div>
      <div class="col-12 pt-3">
        <h5 class="text-success">Selecione o Tempo de Descanso</h5>
      </div>
      <div class="col">
        <label for="" class="text-success"> Horas: </label>
        <select class="custom-select" id="inlineFormCustomSelect" v-model="horasDescanso">
            <option value="00" selected>00</option>
            <option v-for="hora in listaHoras" :key="hora"  :value="hora">{{hora}}</option>
        </select>
      </div>
      <div class="col">
        <label for="" class="text-success"> Minutos: </label>
        <select class="custom-select" id="inlineFormCustomSelect" v-model="minutosDescanso">
          <option value="00" selected>00</option>
          <option v-for="minuto in lista_Min_Seg" :key="minuto"  :value="minuto">{{minuto}}</option>
        </select>
      </div>
      <div class="col">
        <label for="" class="text-success"> Segundos: </label>
        <select class="custom-select" id="inlineFormCustomSelect" v-model="segundosDescanso">
          <option value="00" selected>00</option>
          <option v-for="segundo in lista_Min_Seg" :key="segundo"  :value="segundo">{{segundo}}</option>
        </select>
      </div>
    </div>
    
    <div class="text-success" id="lblTempo" v-if="contadorStatus == 'producao'">
      <span id="intTempo">{{lblTempoProducao}}</span>

    </div>
    <div class="text-success" id="lblTempo" v-if="contadorStatus == 'descanso'">
      <span id="intTempo">{{lblTempoDescanso}}</span>
    </div>
    <div class="row mt-5" v-if="contadorStatus == 'parado'">
        <div class="col">
            <button class="btn btn-outline-success btn-block" @click="iniciarTempo" v-show="contadorStatus == 'parado'"><strong>Iniciar</strong></button>
            <button class="btn btn-outline-success btn-block" @click="pararTempo" v-show="contadorStatus != 'parado'"><strong>Parar</strong></button>
        </div>
    </div>
    <div class="row mt-5" v-else>
        <div class="col" v-if="contadorEsgotado">
            <button class="btn btn-outline-success btn-block" @click="iniciarTempo"><strong>Iniciar {{contadorStatus}}</strong></button>
        </div>
        <div class="col">
            <button class="btn btn-outline-success btn-block" @click="pararTempo"><strong>Parar</strong></button>
        </div>
    </div>
  </div>
</template>

<script>
export default {
    data(){
        return{
        horasProducao:'00',
        minutosProducao:'00',
        segundosProducao:'00',
        horasDescanso:'00',
        minutosDescanso:'00',
        segundosDescanso:'00',
        lblTempoProducao:'',
        lblTempoDescanso:'',
        file:[],
        sound: '',
        soundState:'',
        contadorEsgotado:false,
        contadorStatus:'parado',
        interval:null
        }
    },
    computed:{
        listaHoras(){
            let lista = []
            for(let i=1;i<24;i++){
                (i<10)? lista.push("0"+i.toString()) : lista.push(i.toString())
            }
            
            return lista
        },
        lista_Min_Seg(){
            let lista = []
            for(let i=1;i<60;i++){
                (i<10)? lista.push("0"+i.toString()) : lista.push(i.toString())
            }
            
            return lista
        },
    },
    mounted(){
        let self = this
        let interval = null
        document.addEventListener('deviceready', function () {
            // Enable background mode
            const options = {
                id: -1,
                title: 'Tecnica Pomodoro',
                text: 'Está rodando em segundo plano',
                hidden: false,
                silent: false,
                sticky: true,
                resume: true,
                foreground: true,
            };

            cordova.plugins.backgroundMode.setDefaults(options);
            cordova.plugins.backgroundMode.enable();

            cordova.plugins.backgroundMode.on('activate', () => {
                cordova.plugins.backgroundMode.disableWebViewOptimizations();
                console.log('background mode activate !!!');
                interval = setInterval(() => {
                //self.count++
                let timer = `${self.horasProducao}:${self.minutosProducao}:${self.segundosProducao}`
                cordova.plugins.backgroundMode.configure({ text:timer  });
            }, 1000);
            });
            cordova.plugins.backgroundMode.on('deactivate', () => {
                console.log('background mode deactivate !!!');
                clearInterval(interval)
            });
        }, false);
    },
    methods:{
        formatPlurals(typeTime){
        let result;
        switch (typeTime) {
            case 'horas':
            result = (this.horasProducao < '02') ? 'hora' : 'horas'
            break;
            
            case 'minutos':
            result = (this.minutosProducao < '02') ? 'minuto' : 'minutos'
            break;
            
            case 'segundos':
            result = (this.segundosProducao < '02') ? 'segundo' : 'segundos'
            break;
        }
        return result
        },
        iniciarTempo(){
            let self = this

            if (self.horasProducao == "00" && self.minutosProducao == "00" && self.segundosProducao == "00") {
                alert('Tempo de produção é obrigatório')
                return
            }
            if (self.horasDescanso == "00" && self.minutosDescanso == "00" && self.segundosDescanso == "00") {
                alert('Tempo de descanso é obrigatório')
                return
            }
            if (this.sound == '') {
                alert('Selecione uma musica para tocar o alarme')
                return 
            }
            self.contadorEsgotado = false
            self.lblTempoProducao = `${this.horasProducao}h ${this.minutosProducao}m ${this.segundosProducao}s`
            $('#selecionarTempo').hide()
            $('#lblTempo').show()
            if (self.contadorStatus == 'producao') {
                self.iniciarTemporizadorProducao()
            } else if (self.contadorStatus == 'descanso') {
                self.iniciarTemporizadorDescanso()
            } else {
                self.contadorStatus = 'producao'
                self.iniciarTemporizadorProducao()
            }
        },
        pararTempo(){
            let self = this
            clearInterval(self.interval)
            $('#lblTempo').fadeOut('slow',function(){
                $('#selecionarTempo').fadeIn('slow')
                self.stopAudio()
                self.contadorEsgotado = false
                self.contadorStatus = 'parado'
            })
        },
        iniciarTemporizadorProducao(){
            let self = this;
            self.stopAudio()
            let tempo = new Date();
            if (this.segundosProducao > 0) {
                tempo.setSeconds(parseInt(tempo.getSeconds()) + parseInt(this.segundosProducao))
            }
            if (this.minutosProducao > 0) {
                tempo.setMinutes(parseInt(tempo.getMinutes()) + parseInt(this.minutosProducao))
            }
            if (this.horasProducao > 0) {
                tempo.setHours(parseInt(tempo.getHours()) + parseInt(this.horasProducao))
            }
            this.interval = setInterval(function() {

            // Get today's date and time
            let agora = new Date();
            // Find the distance between now and the count down date
            let distance = tempo - agora;
                
            // Time calculations for days, hours, minutes and seconds
            let hours = Math.floor((distance % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
            let minutes = Math.floor((distance % (1000 * 60 * 60)) / (1000 * 60));
            let seconds = Math.floor((distance % (1000 * 60)) / 1000);
            hours = (hours < 10)?`0${hours}`:hours;
            minutes = (minutes < 10)?`0${minutes}`:minutes;
            seconds = (seconds < 10)?`0${seconds}`:seconds;
            // Output the result in an element with id="demo"
            self.lblTempoProducao = `${hours}h ${minutes}m ${seconds}s`
            // If the count down is over, write some text 
            if (hours == '00' && minutes == '00' && seconds == '00') {
                clearInterval(self.interval);
                self.contadorEsgotado = true
                self.contadorStatus = 'descanso'
                self.lblTempoProducao = "00h 00m 00s"
                document.addEventListener('deviceready', function () {
                    cordova.plugins.backgroundMode.moveToForeground();
                }, false);
                let audio = document.getElementById('myAudio')
                audio.currentTime = 0
                self.playAudio()
            }
            }, 100);
        },
        iniciarTemporizadorDescanso(){
            let self = this;
            self.stopAudio()
            let tempo = new Date();
            if (this.segundosDescanso > 0) {
                tempo.setSeconds(parseInt(tempo.getSeconds()) + parseInt(this.segundosDescanso))
            }
            if (this.minutosDescanso > 0) {
                tempo.setMinutes(parseInt(tempo.getMinutes()) + parseInt(this.minutosDescanso))
            }
            if (this.horasDescanso > 0) {
                tempo.setHours(parseInt(tempo.getHours()) + parseInt(this.horasDescanso))
            }
            this.interval = setInterval(function() {

            // Get today's date and time
            let agora = new Date();

            // Find the distance between now and the count down date
            let distance = tempo - agora;

            // Time calculations for days, hours, minutes and seconds
            let hours = Math.floor((distance % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
            let minutes = Math.floor((distance % (1000 * 60 * 60)) / (1000 * 60));
            let seconds = Math.floor((distance % (1000 * 60)) / 1000);
            hours = (hours < 10)?`0${hours}`:hours;
            minutes = (minutes < 10)?`0${minutes}`:minutes;
            seconds = (seconds < 10)?`0${seconds}`:seconds;
            // Output the result in an element with id="demo"
            self.lblTempoDescanso = `${hours}h ${minutes}m ${seconds}s`
            // If the count down is over, write some text 
            if (hours == '00' && minutes == '00' && seconds == '00') {
                clearInterval(self.interval);
                self.contadorEsgotado = true
                self.contadorStatus = 'producao'
                self.lblTempoDescanso = "00h 00m 00s"
                document.addEventListener('deviceready', function () {
                    cordova.plugins.backgroundMode.moveToForeground();
                }, false);
                let audio = document.getElementById('myAudio')
                audio.currentTime = 0
                self.playAudio()
            }
            }, 100);
        },
        playAudio() {
            let audio = document.getElementById('myAudio')
            this.soundState = 'play'
            audio.play()
        },
        stopAudio(){
            let audio = document.getElementById('myAudio')
            audio.removeAttribute('src')
            audio.removeAttribute('controls')
            this.soundState = 'pause'
            audio.pause();
            audio.currentTime = 0
        },
        musicChange(e){
            if(e.target.files.length == 0){
                this.sound = ''
                return
            }
            this.sound = e.target.files[0].name
            this.file = e.target.files[0]
            this.getFile()
        },
        getFile(){
            let self = this
            let audio = document.getElementById('myAudio')
            let audioObj = new Audio(URL.createObjectURL(this.file));
            URL.revokeObjectURL(audioObj)
            audio.src = audioObj.src
            self.soundState = 'pause'
            //self.playAudio()
        },
    }
}
</script>

<style scoped>
#lblTempo{
  margin: 10px 0;
}

#intTempo{
  font-size: 1.5em;
}

#strTempo{
  font-size: 1.1em;
}
#player button{
    background: transparent;
    border: none;
    color: #28A745;
    display: flex;
    margin: 0;
    padding: 0;
}
#strPlayerTime{
    position: absolute;
    left: 65%;
    top: 5px;
}
#playerTimeSeek{
    width: 55%;
}

</style>
