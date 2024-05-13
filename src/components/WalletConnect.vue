<template>
  <div>
    <div v-if="currentAccount">walletAddress: {{ currentAccount?.address }}
    </div>
    <button v-if="!currentAccount" @click="connectWallet">Connect Wallet</button>
    <button v-else @click="disconnectWallet">Disconnect Wallet</button>
  </div>
</template>

<script setup ts>
import { ref } from 'vue'
import { http, createConfig, connect, getAccount, disconnect } from '@wagmi/core'
import { mainnet, sepolia } from '@wagmi/core/chains'
import { injected, connectors } from '@wagmi/connectors'

console.log(connectors)

const config = createConfig({
  chains: [mainnet, sepolia],
  connectors: [
    injected(),
  ],
  transports: {
    [mainnet.id]: http(),
    [sepolia.id]: http(),
  },
})
const currentAccount = ref()
const currentConnector = ref()

const connectWallet = async () => {
  await connect(config, { connector: injected() }).then(() => {
    const account = getAccount(config)
    currentAccount.value = account
    currentConnector.value = account.connector
    console.log('account...', account);
  })
}

const disconnectWallet = async () => {
  await disconnect(config, currentConnector).then(() => {
    currentAccount.value = null
    currentConnector.value = null
  })
}

</script>
