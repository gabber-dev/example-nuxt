<template>
  <div>
    <!-- Connect Button -->
    <div v-if="!session">
      <button @click="initializeSession">Connect</button>
    </div>

    <!-- Session Interface -->
    <div v-else>
      <!-- Connection and Agent States -->
      <p>Connection State: {{ connectionState }}</p>
      <p>Agent State: {{ agentState }}</p>

      <!-- Text Input for Sending Messages -->
      <div>
        <input
          v-model="inputText"
          type="text"
          placeholder="Type a message"
          @keydown.enter="sendMessage"
        />
        <button @click="sendMessage">Send</button>
      </div>

      <!-- Toggle Microphone -->
      <div>
        <button @click="toggleMicrophone">
          {{ microphoneEnabled ? "Disable" : "Enable" }} Microphone
        </button>
      </div>

      <!-- Agent Visualizer -->
      <div>
        <h3>Agent Visualizer:</h3>
        <div style="height: 40px; width: 100px;">
        <AudioVisualizer
          :frequencyBands="agentVolumeBands"
          barColor="#00ff00"
          :barSpacing="2"
        />
        </div>
      </div>

      <!-- Chat Messages -->
      <div>
        <h3>Messages:</h3>
        <ul>
          <li v-for="(message, index) in messages" :key="index">
            <strong>{{ message.agent ? "Agent" : "User" }}:</strong> {{ message.text }}
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script lang="ts">

import { Session } from 'gabber-client-core';
import type { AgentState, ConnectionState, SessionMessage } from 'gabber-client-core';
import axios from 'axios';


const GABBER_LLM = '21892bb9-9809-4b6f-8c3e-e40093069f04' // Gabber SFW LLM
const GABBER_VOICE = '7e11925d-0a88-4417-9fd4-5a6d728817ec' // Gentle British Female (En)

export default {
  data() {
    return {
      session: null as Session | null, // Will hold the initialized Session object
      agentState: "warmup" as AgentState,
      connectionState: "disconnected" as ConnectionState,
      messages: [] as SessionMessage[],
      agentVolumeBands: [] as number[],
      microphoneEnabled: false,

      inputText: "",
    };
  },
  methods: {
    async toggleMicrophone() {
      if (this.session) {
        this.session.setMicrophoneEnabled(!this.microphoneEnabled);
      }
    },
    async sendMessage() {
      if (this.session && this.inputText.trim() !== "") {
        this.session.sendChatMessage({text: this.inputText});
        this.inputText = ""; // Clear the input field
      }
    },
    async initializeSession() {
      try {
        // Call an endpoint to fetch session data
        const response = await axios.get('http://localhost:4000/token');
        const { token } = response.data;
        console.log('Token:', token);

        // Initialize the Session object
        this.session = new Session({
          onConnectionStateChanged: (state) => {
            this.connectionState = state;
          },
          onAgentStateChanged: (state) => {
            this.agentState = state;
          },
          onAgentError: (error) => {
            console.error('Agent error:', error);
          },
          onAgentVolumeChanged: (bands, volume) => {
            this.agentVolumeBands = bands;
          },
          onCanPlayAudioChanged: (canPlayAudio) => {},
          onMessagesChanged: (messages) => {
            this.messages = [...messages];
          },
          onMicrophoneChanged: (microphone) => {
            this.microphoneEnabled = microphone;
          },
          onRemainingSecondsChanged: (remainingSeconds) => {},
          onUserVolumeChanged: (volume) => {},
        })
        this.session.connect({
          token,
          sessionConnectOptions: {
            llm: GABBER_LLM,
            voice_override: GABBER_VOICE,
            history: [
              {
                role: "system",
                content: "You're a smart woman in your 30s talking to me in the coffee shop.",
              },
            ],
          },
        });
        console.log('Session initialized:', this.session);
      } catch (error) {
        console.error('Error initializing session:', error);
      }
    },
  },
};
</script>