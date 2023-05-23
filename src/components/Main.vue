<script setup lang="ts">
</script>

<template>
    <div id="main" :class="wrongAnswer ? 'wrong-answer' : ''">
        <div id="question">
            
            <h3 id="currentQuestion">
                {{ currentQuestion ? currentQuestion : randomQuestion() }}
            </h3>
            <p :style="wrongAnswer ? '' : 'opacity: 0'">{{ hangulJson[currentQuestion] }}</p>
        </div>
        <input id="answer" type="text" :placeholder="lang != 'en' ? '로마자를 입력하세요' : 'Please enter the romanization'" v-model="answer" />
    </div>
</template>

<script lang="ts">
import hangulJson from '../config/hangul.json';
export default {
    created() {
        document.addEventListener('keydown', (e) => {
            if (e.key !== "Enter" || !this.answer) return;
            this.verifyAnswer();
        })
    },
    data() {
        return {
            hangulJson: this.buildHangulJson(hangulJson),
            currentQuestion: "",
            answer: "",
            wrongAnswer: false,
            leeches: []
        }
    },
    props: ['lang', 'options'],
    methods: {
        buildHangulJson(rawJson: any) {
            let resultJson: Object = {};

            for (let objectName in rawJson) {
                if (!this.options[objectName]) continue;
                Object.assign(resultJson, rawJson[objectName]);
            }

            console.log(resultJson);
            return resultJson;
        },
        randomQuestion() {
            const generatedQuestion = Object.keys(this.hangulJson)[Object.keys(this.hangulJson).length * Math.random() << 0];
            if (!generatedQuestion) return this.currentQuestion = this.lang !== "en" ? "" : "N/A";
            this.currentQuestion = generatedQuestion;
        },
        verifyAnswer() {
            this.answer = this.answer.toLowerCase();
            if (!this.answer || !this.currentQuestion || this.answer !== this.hangulJson[this.currentQuestion]) return this.handleWrongAnswer();
            this.handleCorrectAnswer();
        },
        handleWrongAnswer() {
            this.wrongAnswer = true;
        },
        handleCorrectAnswer() {
            this.wrongAnswer = false;
            this.answer = "";
            this.randomQuestion();
        }
    }
}
</script>

<style scoped>
#main {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 2rem;
}

.wrong-answer {
    color: red;
}

#question {
    display: flex;
    flex-direction: column;
    place-items: center;
    & h3 {
        font-weight: 800;
        font-size: 6rem;
    }
    & p {
        font-size: 2rem;
    }
}

input {
    width: 75%;
    height: 10%;
    font-size: 1rem;
    color: var(--color-text);
    background-color: var(--color-border);
    border-radius: 1rem;
    padding: 0 1rem;
}
</style>