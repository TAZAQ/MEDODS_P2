<template>
    <div class="simon-wrapper">
        <div class="simon">
            <div class="simon__button" @click="onSimonButtonClick(0)" id="0"> </div>
            <div class="simon__button" @click="onSimonButtonClick(1)" id="1"> </div>
            <div class="simon__button" @click="onSimonButtonClick(3)" id="3"> </div>
            <div class="simon__button" @click="onSimonButtonClick(2)" id="2"> </div>
        </div>


        <div class="simon-manage">
            <h1>Уровень</h1>
            <select id="id_level" v-model="level">
                <option value="1500" selected>Лёгкий</option>
                <option value="1000">Средний</option>
                <option value="400">Тяжёлый</option>
            </select>

            <button type="button" @click="onStart">Старт</button>

            <p>{{ simon_message }}</p>

            <span class="simon-manage--round">{{round}}</span>
        </div>
    </div>
</template>

<script>
    export default {
        name: "Simon",
        data() {
            return {
                level: "1500",
                queue: [],
                round: 0,
                user_click_count: -1,
                is_playing: false,
                simon_message: 'Начните играть!'
            }
        },
        methods: {
            onStart: function() {
                this.queue = [];
                this.round = 0;
                this.is_playing = false;

                this.roundStart();
            },
            roundStart: function() {
                // "обнуление" кликов
                this.user_click_count = -1;

                this.pushToQueue();
                this.roundAnimate();
            },
            roundAnimate: function() {
                this.round++;

                // дезактивация кнопок
                this.is_playing = false;
                this.simon_message = 'Слушайте';

                let _this = this;

                // пауза 1000мс перед раундом
                setTimeout(function () {

                    // проигрывание
                    let i = 0;
                    let round = setInterval(function () {
                            _this.playAudioAndLight(_this.queue[i]);

                            i++;
                            if (i >= _this.queue.length) {
                                clearInterval(round);

                                // активация кнопок
                                _this.simon_message = 'Нажимайте';
                                _this.is_playing = true;
                            }
                        },
                        +_this.level
                    );

                }, 1000);


            },
            playAudioAndLight: function(audioId) {
                (new Audio(`sounds/${audioId}.mp3`)).play();

                // подсветка
                let simon_btn = document.getElementById(audioId);
                simon_btn.style.opacity = 1;
                setTimeout(function () {
                    simon_btn.style.opacity = 0.3;
                }, 300);
            },
            pushToQueue: function() {
                this.queue.push(Math.floor((Math.random()*4)));
            },
            onSimonButtonClick: function (buttonId) {
                if ( !this.is_playing ) return;
                this.user_click_count++;
                this.playAudioAndLight(buttonId);

                // проверка на правильность нажатия
                if (buttonId !== this.queue[this.user_click_count]) {
                    this.onFinish();
                    return;
                }

                // если последняя
                if (this.user_click_count + 1 === this.queue.length) {
                    this.roundStart();
                }
            },
            onFinish: function() {
                this.is_playing = false;
                this.simon_message = `Вы проиграли на ${this.round} раунде`

                let _this = this;
                setTimeout(function () {
                    _this.simon_message = 'Ещё разок?';
                    _this.round = 0;
                }, 10000);

            },
        }
    }
</script>