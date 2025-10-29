<template>
  <svg aria-hidden="true" focusable="false" style="position:absolute;width:0;height:0;overflow:hidden">
    <defs>
      <symbol id="icon-copy" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
        <rect x="9" y="9" width="13" height="13" rx="2" ry="2"></rect>
        <path d="M5 15V5a2 2 0 0 1 2-2h10"></path>
      </symbol>
      <symbol id="icon-check" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
        <path d="M20 6L9 17l-5-5" stroke-linecap="round" stroke-linejoin="round"></path>
      </symbol>
    </defs>
  </svg>

  <!-- OBOL Disclaimer -->
  <div v-if="showDisclaimer" class="fixed inset-0 bg-black/70 flex items-center justify-center z-50" role="dialog"
    aria-modal="true" aria-labelledby="obol-disclaimer-title" aria-describedby="obol-disclaimer-desc"
    @click.self="closeDisclaimer" @keydown.esc="closeDisclaimer">
    <div class="bg-surface rounded-2xl p-6 shadow-xl max-w-lg w-full border border-slate-700 outline-none">
      <div class="flex flex-col items-center text-center">
        <img src="/ObolLogo.svg" alt="OBOL logo" class="h-12 w-12 mb-4 shrink-0" />
        <h3 id="obol-disclaimer-title" class="text-xl font-semibold text-foreground mb-2">
          OBOL - Confidential Lending (Proof of Concept)
        </h3>
        <div id="obol-disclaimer-desc" class="space-y-4 text-sm text-muted-foreground mb-2">
          <p>
            I’m not a front-end developer; this interface is provided as a
            <span class="font-semibold text-foreground">proof of concept</span> and is
            <span class="font-semibold text-foreground">not production-ready</span>.
          </p>

          <p>
            I built this alone as a <span class="font-semibold text-foreground">side project</span> and it’s hosted on
            my
            <span class="font-semibold text-foreground">personal server</span>. There are no guarantees regarding
            <span class="font-semibold text-foreground">availability, security,</span> or
            <span class="font-semibold text-foreground">ongoing maintenance</span> of this UI or the underlying Obol
            protocol.
          </p>

          <p>
            Please see the <span class="font-semibold text-foreground">Obol GitHub repository</span> and
            <span class="font-semibold text-foreground">Zama’s documentation</span> for protocol details.<br />
          </p>
          <p>
            This interface is an <span class="font-semibold text-foreground">experimental proof-of-concept</span>
            for a confidential lending/borrowing protocol.
            Amounts are encrypted client-side and processed through Zama’s FHE pipeline.
            Performance and UX can differ from traditional systems, and some values shown in the UI may be
            <span class="font-semibold text-foreground">approximations</span> to preserve confidentiality.
          </p>
          <p>
            OBOL relies on  <span class="font-semibold text-foreground">CAMM</span> for price data.
            Learn more in the OBOL repo.
          </p>

          <p class="text-foreground">
            Thanks for your understanding and for trying Obol!.
          </p>
        </div>

        <a :href="OBOL_ENV.BACKEND_GITHUB_URL" target="_blank" rel="noopener noreferrer" class="inline-flex items-center mt-2 px-4 py-2 bg-gradient-to-r from-primary-300 to-primary-200
               text-primary-foreground rounded-md hover:from-primary-400 hover:to-primary-300
               focus:outline-none focus:ring-2 focus:ring-primary focus:ring-offset-2
               focus:ring-offset-surface transition-all duration-200">
          View Obol docs on GitHub
        </a>

        <a :href="OBOL_ENV.FRONTEND_GITHUB_URL" target="_blank" rel="noopener noreferrer"
          class="text-primary hover:underline hover:text-primary-300 mt-2 text-sm">
          View UI docs on GitHub
        </a>


        <p class="text-xs text-muted-foreground mt-4">
          I’m open to collaborations and/or questions:
          <a href="mailto:cy@6ygb.dev" class="text-primary hover:text-primary-300">cy@6ygb.dev</a>
          <br />
          <span class="opacity-70">Powered by</span>
          <a href="https://6ygb.dev" target="_blank" rel="noopener noreferrer"
            class="text-primary hover:text-primary-300"> 6ygb.dev</a>
        </p>

        <div class="flex items-center justify-between w-full mt-6">
          <label for="obol-dont-show"
            class="flex items-center text-xs text-muted-foreground select-none cursor-pointer">
            <input id="obol-dont-show" type="checkbox" v-model="dontShowAgain" class="mr-2 rounded" />
            Don’t show this again
          </label>
          <div class="space-x-2">
            <button type="button" @click="closeDisclaimer" class="btn btn--ghost">Close</button>
            <button type="button" @click="confirmDisclaimer" autofocus class="btn btn--primary">I understand</button>
          </div>
        </div>
      </div>
    </div>
  </div>

  <div v-if="!showDisclaimer" class="min-h-screen bg-zama p-4 text-foreground">

    <!-- MetaMask Not Installed -->
    <div v-if="!isMetaMaskInstalled" class="min-h-screen flex flex-col items-center justify-center">
      <div
        class="max-w-md w-full bg-surface rounded-2xl shadow-xl overflow-hidden p-8 text-center border border-slate-700">
        <div class="mb-6">
          <img src="/metamask_icon.svg" alt="MetaMask" class="h-24 w-24 mx-auto rounded-2xl shadow-inner" />
        </div>
        <h2 class="text-2xl font-bold mb-4">MetaMask Required</h2>
        <p class="text-muted-foreground mb-6">This dApp needs MetaMask to interact with the blockchain.</p>
        <a href="https://metamask.io/download/" target="_blank" rel="noopener noreferrer"
          class="inline-flex items-center gap-2 px-6 py-3 bg-gradient-to-r from-primary-300 to-primary-200 text-primary-foreground rounded-md hover:from-primary-400 hover:to-primary-300 focus:outline-none focus:ring-2 focus:ring-primary focus:ring-offset-2 focus:ring-offset-surface transition-all duration-200 font-medium">
          <img src="/metamask_icon.svg" alt="" class="h-5 w-5" />
          Install MetaMask
        </a>
        <p class="text-muted-foreground text-sm mt-4">After installation, refresh this page.</p>
      </div>
    </div>

    <!-- Wrong Network -->
    <div v-else-if="isWrongNetwork" class="min-h-screen flex flex-col items-center justify-center">
      <div class="max-w-lg w-full bg-surface rounded-2xl shadow-xl overflow-hidden p-8 border border-border">
        <div class="mb-6 text-red-400 text-center">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-20 w-20 mx-auto" fill="none" viewBox="0 0 24 24"
            stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
              d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z" />
          </svg>
        </div>
        <h2 class="text-2xl font-bold mb-4 text-center">Wrong Network</h2>
        <p class="text-muted-foreground mb-6 text-center">Please switch to the required network.</p>

        <div class="bg-gradient-to-r from-slate-800 to-slate-900 rounded-lg p-6 mb-6 border border-slate-700">
          <h3 class="text-lg font-medium text-primary mb-4">Network Details</h3>
          <div class="space-y-3 text-sm">
            <div class="flex items-center justify-between"><span class="text-muted-foreground">Network</span><span
                class="font-semibold">{{ REQUIRED_NETWORK.chainName }}</span></div>
            <div class="flex items-center justify-between"><span class="text-muted-foreground">Chain ID</span><span
                class="font-semibold">{{ REQUIRED_NETWORK.chainId }}</span></div>
            <div class="flex items-center justify-between"><span class="text-muted-foreground">RPC URL</span><span
                class="font-mono truncate max-w-[220px]">{{ REQUIRED_NETWORK.rpcUrl }}</span></div>
            <div class="flex items-center justify-between"><span class="text-muted-foreground">Currency</span><span
                class="font-semibold">{{ REQUIRED_NETWORK.nativeCurrency.symbol }}</span></div>
            <div class="flex items-center justify-between">
              <span class="text-muted-foreground">Explorer</span>
              <a :href="REQUIRED_NETWORK.blockExplorerUrl" target="_blank" class="font-semibold underline">Open</a>
            </div>
          </div>
        </div>

        <div class="flex items-center justify-center gap-3">
          <button @click="switchToRequiredNetwork" class="btn btn--primary">Switch to {{ REQUIRED_NETWORK.chainName
          }}</button>
          <button @click="refreshPage" class="btn btn--ghost">Refresh</button>
        </div>
      </div>
    </div>

    <!-- Main App -->
    <div v-else class="max-w-5xl mx-auto bg-surface rounded-3xl shadow-2xl overflow-hidden border border-border">
      <!-- Header -->
      <div class="bg-gradient-to-r from-[#101522] via-[#0f141d] to-[#0b1017] p-6 md:p-7 border-b border-primary-300/30">
        <div class="flex items-center justify-between gap-4">
          <div class="flex items-center gap-4">
            <img src="/ObolLogo.svg" alt="OBOL Logo" class="h-14 w-14 md:h-16 md:w-16 shrink-0 select-none"
              draggable="false" />
            <div>
              <h1 class="text-2xl md:text-3xl font-bold">OBOL - Confidential Lending</h1>
              <p class="text-sm text-muted-foreground">
                Market: <span class="font-mono">{{ selectedMarketLabel }}</span>
                <span class="mx-2 opacity-60">·</span>
                Oracle: {{ oracleMeta.fresh ? 'Fresh' : 'Stale' }}
              </p>
            </div>
          </div>
          <div v-if="!isConnected">
            <button @click="connectMetaMask" class="btn btn--primary">Connect Wallet</button>
          </div>
          <div v-else
            class="px-4 py-2 rounded-full bg-emerald-900/30 border border-emerald-700 text-emerald-200 text-sm">
            Connected: {{ truncateAddress(account) }}
          </div>
        </div>
      </div>

      <!-- Tabs -->
      <div class="border-b border-border bg-surface">
        <nav class="flex flex-wrap gap-3 p-4">
          <button v-for="tab in tabs" :key="tab.id" @click="activeTab = tab.id"
            class="px-5 py-2.5 rounded-full text-sm font-medium transition-all border" :class="activeTab === tab.id
              ? 'bg-gold text-black border-transparent shadow'
              : 'bg-slate-900/60 border-slate-700 text-slate-300 hover:bg-slate-800'">
            {{ tab.name }}
          </button>
        </nav>
      </div>

      <div v-if="!isConnected" class="bg-amber-900/25 border-b border-amber-700 p-3 text-sm text-amber-200">
        Please connect your wallet to use this application.
      </div>

      <!-- Content -->
      <div class="p-6 bg-surface space-y-6">
        <!-- Market -->
        <section v-if="activeTab === 'market'"
          class="rounded-2xl border border-slate-700 bg-gradient-to-br from-slate-900 to-slate-800 p-5">
          <div class="mb-4">
            <h2 class="text-lg font-semibold text-primary">Select Market & Overview</h2>
            <p class="text-xs text-muted-foreground">OBOL stores only the two market addresses. Collateral & Debt tokens
              are read from the selected market.</p>
          </div>

          <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
            <div class="bg-surface-2 p-4 rounded-lg border border-slate-700 md:col-span-1">
              <label class="text-sm text-muted-foreground">Market</label>
              <select v-model="selectedMarket" @change="loadMarket()"
                class="mt-2 block w-full pl-3 pr-10 py-2 text-base border-slate-700 focus:outline-none focus:ring-primary focus:border-amber-500 sm:text-sm rounded-md bg-surface-2">
                <option value="EURtoUSD">EUR → USD</option>
                <option value="USDtoEUR">USD → EUR</option>
              </select>
              <div class="text-xs text-muted-foreground mt-3">Address:
                <span class="font-mono">{{ truncateAddress(current.marketAddress) }}</span>
                <button
                  class="relative inline-flex items-center justify-center rounded p-1 hover:bg-slate-800 transition"
                  :title="isCopied(current.marketAddress) ? 'Copied!' : 'Copy address'"
                  @click="copyToClipboard(current.marketAddress)">
                  <span v-if="isCopied(current.marketAddress)" class="text-emerald-400 relative">
                    <svg class="h-3.5 w-3.5">
                      <use href="#icon-check" />
                    </svg>
                    <span
                      class="absolute -top-1 -right-1 h-2 w-2 rounded-full bg-emerald-400 opacity-75 animate-ping"></span>
                  </span>
                  <span v-else class="text-slate-300 hover:text-primary">
                    <svg class="h-3.5 w-3.5">
                      <use href="#icon-copy" />
                    </svg>
                  </span>
                </button>
              </div>
            </div>

            <div class="bg-surface-2 p-4 rounded-lg border border-slate-700 md:col-span-2">
              <div class="grid grid-cols-2 gap-4">
                <div>
                  <div class="text-xs text-muted-foreground">Collateral Token</div>
                  <div class="font-mono text-sm break-all">
                    {{ truncateAddress(current.collat) }}
                    <span class="ml-2 text-primary font-semibold">{{ tokenSymbols.collat }}</span>
                  </div>
                </div>
                <div>
                  <div class="text-xs text-muted-foreground">Debt Token</div>
                  <div class="font-mono text-sm break-all">
                    {{ truncateAddress(current.debt) }}
                    <span class="ml-2 text-primary font-semibold">{{ tokenSymbols.debt }}</span>
                  </div>
                </div>
                <div>
                  <div class="text-xs text-muted-foreground">
                    Price ({{ priceUnits.left }} per 1 {{ priceUnits.right }})
                  </div>
                  <div class="text-lg font-semibold">
                    {{ format6(scalars.price6) }} {{ priceUnits.left }} / 1 {{ priceUnits.right }}
                  </div>
                </div>
                <div>
                  <div class="text-xs text-muted-foreground">Seize per unit (liq)</div>
                  <div class="text-lg font-semibold">
                    {{ format6(scalars.seizePerUnit6) }}
                    {{ priceUnits.left }} / 1 {{ priceUnits.right }}
                  </div>
                </div>
                <div>
                  <div class="text-xs text-muted-foreground">Borrow Index</div>
                  <div class="font-mono">{{ format6(scalars.borrowIndex6) }}</div>
                </div>
                <div>
                  <div class="text-xs text-muted-foreground">Supply Index</div>
                  <div class="font-mono">{{ format6(scalars.supplyIndex6) }}</div>
                </div>
                <div>
                  <div class="text-xs text-muted-foreground">Borrow APR</div>
                  <div class="font-semibold">{{ formatAPR(scalars.borrowApr6) }}</div>
                </div>
                <div>
                  <div class="text-xs text-muted-foreground">Supply APR</div>
                  <div class="font-semibold">{{ formatAPR(scalars.supplyApr6) }}</div>
                </div>
              </div>

              <div class="mt-4">
                <button class="btn btn--primary" :disabled="pending.refreshMarket" @click="refreshMarket">Refresh Market
                  Data</button>
              </div>
            </div>
          </div>
        </section>

        <!-- Operators -->
        <section v-if="activeTab === 'operators'"
          class="rounded-2xl border border-slate-700 bg-gradient-to-br from-slate-900 to-slate-800 p-5">
          <div class="mb-4">
            <h2 class="text-lg font-semibold text-primary">Operator Permissions</h2>
            <p class="text-xs text-muted-foreground">Grant operator status so the market can move your encrypted tokens
              for specific actions.</p>
          </div>

          <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
            <div class="bg-surface-2 p-4 rounded-lg border border-slate-700">
              <div class="font-medium">Collateral Token</div>
              <div class="text-xs text-muted-foreground mb-2">{{ tokenSymbols.collat }} - {{
                truncateAddress(current.collat) }}</div>
              <div class="text-xs text-muted-foreground">Required for <span class="text-foreground">add/remove
                  collateral</span>.</div>
              <div class="text-xs text-muted-foreground mt-2">Duration: {{ Math.round(OPERATOR_SECS / 60) }} minutes
              </div>
              <button class="btn btn--primary w-full mt-3" :disabled="pending.operator || !isConnected"
                @click="setOperatorOn(current.collat, current.marketAddress)">Grant Operator</button>
            </div>

            <div class="bg-surface-2 p-4 rounded-lg border border-slate-700">
              <div class="font-medium">Debt Token</div>
              <div class="text-xs text-muted-foreground mb-2">{{ tokenSymbols.debt }} - {{ truncateAddress(current.debt)
              }}</div>
              <div class="text-xs text-muted-foreground">Required for <span class="text-foreground">deposit,
                  repay</span>.</div>
              <div class="text-xs text-muted-foreground mt-2">Duration: {{ Math.round(OPERATOR_SECS / 60) }} minutes
              </div>
              <button class="btn btn--primary w-full mt-3" :disabled="pending.operator || !isConnected"
                @click="setOperatorOn(current.debt, current.marketAddress)">Grant Operator</button>
            </div>

            <div class="bg-surface-2 p-4 rounded-lg border border-slate-700">
              <div class="font-medium">Market (oToken)</div>
              <div class="text-xs text-muted-foreground mb-2">{{ truncateAddress(current.marketAddress) }}</div>
              <div class="text-xs text-muted-foreground">Required for <span class="text-foreground">withdraw underlying
                  (redeem)</span>.</div>
              <div class="text-xs text-muted-foreground mt-2">Duration: {{ Math.round(OPERATOR_SECS / 60) }} minutes
              </div>
              <button class="btn btn--primary w-full mt-3" :disabled="pending.operator || !isConnected"
                @click="setOperatorOn(current.marketAddress, current.marketAddress)">Grant Operator</button>
            </div>
          </div>
        </section>

        <!-- Portfolio -->
        <section v-if="activeTab === 'portfolio'"
          class="rounded-2xl border border-slate-700 bg-gradient-to-br from-slate-900 to-slate-800 p-5">
          <div class="mb-4">
            <h2 class="text-lg font-semibold text-primary">My Position
              <span class="ml-2 mb-2 inline-flex items-center px-2 py-0.5 rounded-full border text-xs" :class="userHealth.liquidatable
                ? 'text-red-300 border-red-700 bg-red-900/30'
                : 'text-emerald-300 border-emerald-700 bg-emerald-900/20'">
                {{ userHealth.liquidatable ? 'LIQUIDATABLE' : 'Safe' }}
              </span>
            </h2>
            <p class="text-xs text-muted-foreground">Encrypted balances are allowed to you and decrypted locally.</p>
          </div>

          <div class="grid grid-cols-1 md:grid-cols-4 gap-4">
            <div class="bg-surface-2 p-4 rounded-lg border border-slate-700">
              <div class="text-xs text-muted-foreground">Collateral</div>
              <div class="text-2xl font-bold">{{ userClear.collat }}</div>
              <div class="text-xs text-muted-foreground">{{ tokenSymbols.collat }}</div>
            </div>
            <div class="bg-surface-2 p-4 rounded-lg border border-slate-700">
              <div class="text-xs text-muted-foreground">Debt</div>
              <div class="text-2xl font-bold">{{ userClear.debt }}</div>
              <div class="text-xs text-muted-foreground">{{ tokenSymbols.debt }}</div>
            </div>
            <div class="bg-surface-2 p-4 rounded-lg border border-slate-700">
              <div class="text-xs text-muted-foreground">Max Borrow Headroom</div>
              <div class="text-2xl font-bold">{{ userClear.maxBorrow }}</div>
              <div class="text-xs text-muted-foreground">{{ tokenSymbols.debt }}</div>
            </div>
            <div class="bg-surface-2 p-4 rounded-lg border border-slate-700">
              <div class="text-xs text-muted-foreground">Health Factor</div>

              <div class="text-2xl font-bold mt-1 leading-none">
                {{ format6(userHealth.hfNorm6) }}
              </div>
              <p class="text-xs ">Normalized</p>

              <div class="text-1xl font-bold mt-2 leading-none">
                {{ format6(userHealth.hfRaw6) }}
              </div>
              <p class="text-xs ">Raw</p>
              <div class="mt-2 text-xs text-muted-foreground">
                Raw liq threshold ≈ <span class="text-foreground">{{ format6(userHealth.threshold6) }}</span>
              </div>

            </div>
          </div>

          <div class="mt-4 grid grid-cols-1 md:grid-cols-3 gap-3">
            <button class="btn btn--primary w-full" :disabled="pending.pos || !isConnected"
              @click="refreshFactors">Refresh Factors</button>
            <button class="btn btn--primary w-full" :disabled="pending.pos || !isConnected"
              @click="computeMaxBorrow">Compute Max Borrow</button>
            <button class="btn btn--ghost w-full" :disabled="pending.pos || !isConnected" @click="loadUserPos">Reload
              Position</button>
          </div>
        </section>

        <!-- Supply -->
        <section v-if="activeTab === 'supply'"
          class="rounded-2xl border border-slate-700 bg-gradient-to-br from-slate-900 to-slate-800 p-5">
          <div class="mb-4">
            <h2 class="text-lg font-semibold text-primary">Supply Debt Asset</h2>
            <p class="text-xs text-muted-foreground">Deposit the debt token to mint confidential oTokens, or redeem to
              withdraw underlying.</p>
          </div>

          <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
            <div class="bg-surface-2 p-4 rounded-lg border border-slate-700">
              <label class="text-sm text-muted-foreground">Deposit amount ({{ tokenSymbols.debt }})</label>
              <input v-model.number="supply.deposit" type="number"
                class="mt-1 w-full px-3 py-2 border rounded-md border-slate-700 bg-surface-2 placeholder-slate-500"
                placeholder="e.g. 1000" />
              <button class="btn btn--primary w-full mt-3" :disabled="pending.supply || !isConnected"
                @click="depositDebt">Deposit</button>
            </div>

            <div class="bg-surface-2 p-4 rounded-lg border border-slate-700">
              <label class="text-sm text-muted-foreground">Withdraw (redeem shares)</label>
              <input v-model.number="supply.withdraw" type="number"
                class="mt-1 w-full px-3 py-2 border rounded-md border-slate-700 bg-surface-2 placeholder-slate-500"
                placeholder="shares (1e6 scale assumption)" />
              <button class="btn btn--primary w-full mt-3" :disabled="pending.supply || !isConnected"
                @click="withdrawDebt">Withdraw</button>
              <p class="text-xs text-muted-foreground mt-2">Clamped by liquidity above pool reserve.</p>
            </div>
          </div>
        </section>

        <!-- Collateral -->
        <section v-if="activeTab === 'collateral'"
          class="rounded-2xl border border-slate-700 bg-gradient-to-br from-slate-900 to-slate-800 p-5">
          <div class="mb-4">
            <h2 class="text-lg font-semibold text-primary">Manage Collateral</h2>
            <p class="text-xs text-muted-foreground">Amounts are encrypted. Removing collateral is clamped to keep your
              position safe.</p>
          </div>

          <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
            <div class="bg-surface-2 p-4 rounded-lg border border-slate-700">
              <label class="text-sm text-muted-foreground">Add collateral ({{ tokenSymbols.collat }})</label>
              <input v-model.number="collat.add" type="number"
                class="mt-1 w-full px-3 py-2 border rounded-md border-slate-700 bg-surface-2 placeholder-slate-500"
                placeholder="e.g. 500" />
              <button class="btn btn--primary w-full mt-3" :disabled="pending.collat || !isConnected"
                @click="addCollateral">Add</button>
            </div>

            <div class="bg-surface-2 p-4 rounded-lg border border-slate-700">
              <label class="text-sm text-muted-foreground">Remove collateral (requested)</label>
              <input v-model.number="collat.remove" type="number"
                class="mt-1 w-full px-3 py-2 border rounded-md border-slate-700 bg-surface-2 placeholder-slate-500"
                placeholder="e.g. 100" />
              <button class="btn btn--primary w-full mt-3" :disabled="pending.collat || !isConnected"
                @click="removeCollateral">Remove</button>
              <p class="text-xs text-muted-foreground mt-2">Request is internally clamped to a safe amount.</p>
            </div>
          </div>
        </section>

        <!-- Borrow -->
        <section v-if="activeTab === 'borrow'"
          class="rounded-2xl border border-slate-700 bg-gradient-to-br from-slate-900 to-slate-800 p-5">
          <div class="mb-4">
            <h2 class="text-lg font-semibold text-primary">Borrow / Repay</h2>
            <p class="text-xs text-muted-foreground">Borrow up to your encrypted max headroom; repay reduces encrypted
              principal.</p>
          </div>

          <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
            <div class="bg-surface-2 p-4 rounded-lg border border-slate-700">
              <label class="text-sm text-muted-foreground">Borrow amount ({{ tokenSymbols.debt }})</label>
              <input v-model.number="loan.borrow" type="number"
                class="mt-1 w-full px-3 py-2 border rounded-md border-slate-700 bg-surface-2 placeholder-slate-500"
                placeholder="e.g. 200" />
              <button class="btn btn--primary w-full mt-3" :disabled="pending.borrow || !isConnected"
                @click="doBorrow">Borrow</button>
            </div>

            <div class="bg-surface-2 p-4 rounded-lg border border-slate-700">
              <label class="text-sm text-muted-foreground">Repay amount ({{ tokenSymbols.debt }})</label>
              <input v-model.number="loan.repay" type="number"
                class="mt-1 w-full px-3 py-2 border rounded-md border-slate-700 bg-surface-2 placeholder-slate-500"
                placeholder="e.g. 50" />
              <button class="btn btn--primary w-full mt-3" :disabled="pending.borrow || !isConnected"
                @click="doRepay">Repay</button>
            </div>
          </div>
        </section>

        <!-- Oracle -->
        <section v-if="activeTab === 'oracle'"
          class="rounded-2xl border border-slate-700 bg-gradient-to-br from-slate-900 to-slate-800 p-5">
          <div class="mb-4">
            <h2 class="text-lg font-semibold text-primary">Oracle</h2>
            <p class="text-xs text-muted-foreground">
              Refresh and inspect oracle status. Some update methods may be restricted to a relayer; the UI will show
              errors if you’re not authorized.
            </p>
          </div>

          <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
            <div class="bg-surface-2 p-4 rounded-lg border border-slate-700">
              <div class="text-xs text-muted-foreground">Oracle Address</div>
              <div class="font-mono">{{ truncateAddress(current.oracle) }}</div>

              <div class="mt-3 grid grid-cols-2 gap-3">
                <div>
                  <div class="text-xs text-muted-foreground">USD per 1 EUR (raw)</div>
                  <div class="text-lg font-semibold">{{ format6(oracleMeta.usdPerEur6) }} USD / 1 EUR</div>
                </div>
                <div>
                  <div class="text-xs text-muted-foreground">EUR per 1 USD (inverse)</div>
                  <div class="text-lg font-semibold">{{ format6(oracleMeta.eurPerUsd6) }} EUR / 1 USD</div>
                </div>
                <div>
                  <div class="text-xs text-muted-foreground">Fresh</div>
                  <div class="text-lg font-semibold">{{ oracleMeta.fresh ? 'Yes' : 'No' }}</div>
                </div>
                <div>
                  <div class="text-xs text-muted-foreground">Price used by market</div>
                  <div class="text-lg font-semibold">
                    {{ format6(scalars.price6) }}
                    {{ priceUnits.left }} / 1 {{ priceUnits.right }}
                  </div>
                </div>
                <div>
                  <div class="text-xs text-muted-foreground">Last Update (UTC)</div>
                  <div class="text-sm">{{ oracleMeta.lastUpdateTs ? fmtTsUTC(oracleMeta.lastUpdateTs) : '-' }}</div>
                </div>
                <div>
                  <div class="text-xs text-muted-foreground">Last Update (Local)</div>
                  <div class="text-sm">{{ oracleMeta.lastUpdateTs ? fmtTsLocal(oracleMeta.lastUpdateTs) : '-' }}</div>
                </div>
                <div>
                  <div class="text-xs text-muted-foreground">Stale TTL</div>
                  <div class="text-sm">{{ oracleMeta.staleTtl || '-' }} s</div>
                </div>
              </div>

              <div class="mt-4 flex gap-3">
                <button class="btn btn--primary w-full" :disabled="pending.oracle"
                  @click="readOracleAndRecompute">Refresh
                  Oracle</button>
              </div>
              <p v-if="oracleMeta.msg" class="text-xs text-muted-foreground mt-2">{{ oracleMeta.msg }}</p>
            </div>

            <div class="bg-surface-2 p-4 rounded-lg border border-slate-700">
              <div class="font-medium">Price Direction</div>
              <p class="text-xs text-muted-foreground mt-1">
                {{ scalars.directionLabel }} · The market price is computed as <span class="text-foreground">collateral
                  units per 1 debt unit</span> (1e6), derived from the oracle following your contract rule.
              </p>
              <div class="mt-3">
                <div class="text-xs text-muted-foreground mb-1">Liquidation Seize Rate</div>
                <div class="text-lg font-semibold">
                  {{ format6(scalars.seizePerUnit6) }}
                  {{ priceUnits.left }} / 1 {{ priceUnits.right }}
                </div>
                <p class="text-xs text-muted-foreground">= price × (1 + bonus)</p>
              </div>
            </div>
          </div>
        </section>

        <!-- Tokens -->
        <section v-if="activeTab === 'tokens'"
          class="rounded-2xl border border-slate-700 bg-gradient-to-br from-slate-900 to-slate-800 p-5">

          <div class="mb-4">
            <h2 class="text-lg font-semibold text-primary">Tokens</h2>
            <p class="text-xs text-muted-foreground">
              Decrypt your confidential token balances and claim test airdrops.
            </p>
          </div>

          <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
            <!-- Collateral token card -->
            <div class="bg-surface-2 p-4 rounded-lg border border-slate-700">
              <div class="flex items-center justify-between">
                <div class="font-medium">{{ tokenSymbols.collat }} (Collateral)</div>
                <div class="text-xs text-muted-foreground font-mono">
                  {{ truncateAddress(current.collat) }}
                </div>
              </div>

              <div class="mt-3">
                <div class="text-xs text-muted-foreground">Decrypted Balance</div>
                <div class="text-2xl font-bold">{{ tokenBalances.collat }}</div>
                <div class="text-xs text-muted-foreground">{{ tokenSymbols.collat }}</div>
              </div>

              <div class="mt-4 flex gap-2">

                <button class="btn btn--ghost w-full" :disabled="pending.tokens || !isConnected"
                  @click="claimTokenAirdrop(current.collat, tokenSymbols.collat)">
                  Claim Airdrop
                </button>
              </div>
            </div>

            <!-- Debt token card -->
            <div class="bg-surface-2 p-4 rounded-lg border border-slate-700">
              <div class="flex items-center justify-between">
                <div class="font-medium">{{ tokenSymbols.debt }} (Debt)</div>
                <div class="text-xs text-muted-foreground font-mono">
                  {{ truncateAddress(current.debt) }}
                </div>
              </div>

              <div class="mt-3">
                <div class="text-xs text-muted-foreground">Decrypted Balance</div>
                <div class="text-2xl font-bold">{{ tokenBalances.debt }}</div>
                <div class="text-xs text-muted-foreground">{{ tokenSymbols.debt }}</div>
              </div>

              <div class="mt-4 flex gap-2">
                <button class="btn btn--ghost w-full" :disabled="pending.tokens || !isConnected"
                  @click="claimTokenAirdrop(current.debt, tokenSymbols.debt)">
                  Claim Airdrop
                </button>
              </div>
            </div>
          </div>

          <div class="mt-4 flex justify-center">
            <button class="btn btn--primary w-full w-3/4" :disabled="pending.tokens || !isConnected"
              @click="decryptTokenBalances()">
              Decrypt Balances
            </button>
          </div>
        </section>

        <!-- Liquidation -->
        <section v-if="activeTab === 'liquidation'"
          class="rounded-2xl border border-slate-700 bg-gradient-to-br from-slate-900 to-slate-800 p-5">
          <div class="mb-4">
            <h2 class="text-lg font-semibold text-primary">Liquidation</h2>
            <p class="text-xs text-muted-foreground">
              Scan liquidatable users, browse active positions, check the health of any address, and perform/claim
              liquidations.
            </p>
          </div>

          <!-- 1) Scan: Liquidatable slice -->
          <div class="bg-surface-2 p-4 rounded-lg border border-slate-700 mb-4">
            <div class="flex items-end gap-3">
              <div>
                <label class="text-xs text-muted-foreground mr-2">Offset</label>
                <input v-model.number="liq.off1" type="number"
                  class="mt-1 w-28 px-3 py-2 border rounded-md border-slate-700 bg-surface-2" />
              </div>
              <div>
                <label class="text-xs text-muted-foreground mr-2">Limit</label>
                <input v-model.number="liq.lim1" type="number"
                  class="mt-1 w-28 px-3 py-2 border rounded-md border-slate-700 bg-surface-2" />
              </div>
              <button class="btn btn--primary" :disabled="liq.scanning || !isConnected" @click="loadLiquidatableSlice">
                Load Liquidatable Slice
              </button>
            </div>

            <div class="mt-3 overflow-x-auto">
              <table class="w-full text-sm">
                <thead class="text-slate-300">
                  <tr>
                    <th class="text-left py-2">Address</th>
                    <th class="text-left py-2">HF (raw)</th>
                    <th class="text-left py-2">HF (norm)</th>
                    <th class="text-left py-2">Copy</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-if="liq.liquidatable.length === 0">
                    <td class="py-2 text-muted-foreground" colspan="4">No liquidatable positions in this slice.</td>
                  </tr>
                  <tr v-for="row in liq.liquidatable" :key="row.addr" class="border-t border-slate-700">
                    <td class="py-2 font-mono">{{ truncateAddress(row.addr) }}</td>
                    <td class="py-2">{{ format6(row.hfRaw6) }}</td>
                    <td class="py-2">{{ format6(row.hfNorm6) }}</td>
                    <td class="py-2">
                      <button class="relative inline-flex items-center justify-center rounded p-1 hover:bg-slate-800"
                        :title="isCopied(row.addr) ? 'Copied!' : 'Copy address'" @click="copyToClipboard(row.addr)">
                        <span v-if="isCopied(row.addr)" class="text-emerald-400 relative">
                          <svg class="h-3.5 w-3.5">
                            <use href="#icon-check" />
                          </svg>
                          <span
                            class="absolute -top-1 -right-1 h-2 w-2 rounded-full bg-emerald-400 opacity-75 animate-ping"></span>
                        </span>
                        <span v-else class="text-slate-300 hover:text-primary">
                          <svg class="h-3.5 w-3.5">
                            <use href="#icon-copy" />
                          </svg>
                        </span>
                      </button>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>

          <!-- 2) Browse: Active positions -->
          <div class="bg-surface-2 p-4 rounded-lg border border-slate-700 mb-4">
            <div class="flex items-end gap-3">
              <div>
                <label class="text-xs text-muted-foreground mr-2">Offset</label>
                <input v-model.number="liq.off2" type="number"
                  class="mt-1 w-28 px-3 py-2 border rounded-md border-slate-700 bg-surface-2" />
              </div>
              <div>
                <label class="text-xs text-muted-foreground mr-2">Limit</label>
                <input v-model.number="liq.lim2" type="number"
                  class="mt-1 w-28 px-3 py-2 border rounded-md border-slate-700 bg-surface-2" />
              </div>
              <button class="btn btn--ghost" :disabled="liq.browsing || !isConnected" @click="loadActiveRange">
                Load Active Slice
              </button>
            </div>

            <div class="mt-3 overflow-x-auto">
              <table class="w-full text-sm">
                <thead class="text-slate-300">
                  <tr>
                    <th class="text-left py-2">Address</th>
                    <th class="text-left py-2">HF (raw)</th>
                    <th class="text-left py-2">HF (norm)</th>
                    <th class="text-left py-2">Liquidatable?</th>
                    <th class="text-left py-2">Copy</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-if="liq.active.length === 0">
                    <td class="py-2 text-muted-foreground" colspan="5">No active borrowers in this slice.</td>
                  </tr>
                  <tr v-for="row in liq.active" :key="row.addr" class="border-t border-slate-700">
                    <td class="py-2 font-mono">{{ truncateAddress(row.addr) }}</td>
                    <td class="py-2">{{ format6(row.hfRaw6) }}</td>
                    <td class="py-2">{{ format6(row.hfNorm6) }}</td>
                    <td class="py-2">
                      <span :class="row.liquidatable ? 'text-red-300' : 'text-emerald-300'">
                        {{ row.liquidatable ? 'Yes' : 'No' }}
                      </span>
                    </td>
                    <td class="py-2">
                      <button class="relative inline-flex items-center justify-center rounded p-1 hover:bg-slate-800"
                        :title="isCopied(row.addr) ? 'Copied!' : 'Copy address'" @click="copyToClipboard(row.addr)">
                        <span v-if="isCopied(row.addr)" class="text-emerald-400 relative">
                          <svg class="h-3.5 w-3.5">
                            <use href="#icon-check" />
                          </svg>
                          <span
                            class="absolute -top-1 -right-1 h-2 w-2 rounded-full bg-emerald-400 opacity-75 animate-ping"></span>
                        </span>
                        <span v-else class="text-slate-300 hover:text-primary">
                          <svg class="h-3.5 w-3.5">
                            <use href="#icon-copy" />
                          </svg>
                        </span>
                      </button>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>

          <!-- 3) Check one address -->
          <div class="bg-surface-2 p-4 rounded-lg border border-slate-700 mb-4">
            <div class="grid grid-cols-1 md:grid-cols-3 gap-3 items-end">
              <div class="md:col-span-2">
                <label class="text-xs text-muted-foreground">Address</label>
                <input v-model="liq.checkAddr" placeholder="0x…"
                  class="mt-1 w-full px-3 py-2 border rounded-md border-slate-700 bg-surface-2" />
              </div>
              <button class="btn btn--primary" :disabled="liq.checking || !isConnected" @click="checkAddressHF">
                Check HF
              </button>
            </div>

            <div v-if="liq.check" class="mt-3 grid grid-cols-1 md:grid-cols-4 gap-3">
              <div class="bg-surface p-3 rounded border border-slate-700">
                <div class="text-xs text-muted-foreground">Address</div>
                <div class="font-mono">{{ truncateAddress(liq.check.addr) }}</div>
              </div>
              <div class="bg-surface p-3 rounded border border-slate-700">
                <div class="text-xs text-muted-foreground">HF (raw)</div>
                <div class="font-semibold">{{ format6(liq.check.hfRaw6) }}</div>
              </div>
              <div class="bg-surface p-3 rounded border border-slate-700">
                <div class="text-xs text-muted-foreground">HF (norm)</div>
                <div class="font-semibold">{{ format6(liq.check.hfNorm6) }}</div>
              </div>
              <div class="bg-surface p-3 rounded border border-slate-700">
                <div class="text-xs text-muted-foreground">Liquidatable?</div>
                <div :class="liq.check.liquidatable ? 'text-red-300' : 'text-emerald-300'">
                  {{ liq.check.liquidatable ? 'Yes' : 'No' }}
                </div>
              </div>
            </div>
          </div>

          <!-- 4) Perform liquidation -->
          <div class="bg-surface-2 p-4 rounded-lg border border-slate-700 mb-4">
            <div class="grid grid-cols-1 md:grid-cols-3 gap-3 items-end">
              <div class="md:col-span-2">
                <label class="text-xs text-muted-foreground">Target address</label>
                <input v-model="liq.target" placeholder="0x…"
                  class="mt-1 w-full px-3 py-2 border rounded-md border-slate-700 bg-surface-2" />
              </div>
              <div>
                <label class="text-xs text-muted-foreground">Repay amount ({{ tokenSymbols.debt }})</label>
                <input v-model.number="liq.repay" type="number" placeholder="e.g. 50"
                  class="mt-1 w-full px-3 py-2 border rounded-md border-slate-700 bg-surface-2" />
              </div>
            </div>
            <div class="mt-3">
              <button class="btn btn--primary w-full md:w-auto" :disabled="liq.pendingTx || !isConnected"
                @click="performLiquidation">
                Liquidate
              </button>
              <p class="text-xs text-muted-foreground mt-2">Market needs to be set as an operator on {{
                tokenSymbols.debt }} before. If successful, seized collateral is queued. Use “Claim”
                below.</p>
            </div>
          </div>

          <!-- 5) Claim seized collateral -->
          <div class="bg-surface-2 p-4 rounded-lg border border-slate-700">
            <div class="grid grid-cols-1 md:grid-cols-3 gap-3 items-end">
              <div class="md:col-span-2">
                <label class="text-xs text-muted-foreground">Victim address (to claim seized collateral)</label>
                <input v-model="liq.claimUser" placeholder="0x…"
                  class="mt-1 w-full px-3 py-2 border rounded-md border-slate-700 bg-surface-2" />
              </div>
              <button class="btn btn--ghost" :disabled="liq.pendingClaim || !isConnected" @click="claimSeized">
                Claim
              </button>
            </div>
          </div>
        </section>


      </div>



      <!-- Footer addresses -->
      <div class="bg-gradient-to-r from-[#0c1118] to-[#0b0f15] p-4 border-t border-border">
        <div class="flex flex-wrap gap-2 text-xs">
          <div
            class="bg-surface-2 rounded-full px-3 py-1 border border-slate-700 flex items-center gap-2 cursor-pointer"
            @click="copyToClipboard(current.marketAddress)" title="Click to copy Market address">
            <span class="font-semibold text-primary">Market</span>
            <span class="font-mono">{{ truncateAddress(current.marketAddress) }}</span>
            <span class="relative ml-1">
              <span v-if="isCopied(current.marketAddress)" class="text-emerald-400 relative">
                <svg class="h-3.5 w-3.5">
                  <use href="#icon-check" />
                </svg>
                <span
                  class="absolute -top-1 -right-1 h-2 w-2 rounded-full bg-emerald-400 opacity-75 animate-ping"></span>
              </span>
              <span v-else class="text-slate-300 hover:text-primary">
                <svg class="h-3.5 w-3.5">
                  <use href="#icon-copy" />
                </svg>
              </span>
            </span>
          </div>
          <div
            class="bg-surface-2 rounded-full px-3 py-1 border border-slate-700 flex items-center gap-2 cursor-pointer"
            @click="copyToClipboard(current.collat)" title="Click to copy Collateral token">
            <span class="font-semibold text-primary">Collat</span>
            <span class="font-mono">{{ truncateAddress(current.collat) }}</span>
          </div>
          <div
            class="bg-surface-2 rounded-full px-3 py-1 border border-slate-700 flex items-center gap-2 cursor-pointer"
            @click="copyToClipboard(current.debt)" title="Click to copy Debt token">
            <span class="font-semibold text-primary">Debt</span>
            <span class="font-mono">{{ truncateAddress(current.debt) }}</span>
          </div>
          <div
            class="bg-surface-2 rounded-full px-3 py-1 border border-slate-700 flex items-center gap-2 cursor-pointer"
            @click="copyToClipboard(current.oracle)" title="Click to copy Oracle address">
            <span class="font-semibold text-primary">Oracle</span>
            <span class="font-mono">{{ truncateAddress(current.oracle) }}</span>
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- Loading -->
  <div v-if="!blockModals && showLoading" class="fixed inset-0 bg-black/70 flex items-center justify-center z-50"
    @click.self="closeLoading" @keydown.esc="closeLoading">
    <div class="bg-surface rounded-lg p-6 shadow-xl max-w-md w-full border border-slate-700">
      <div class="flex flex-col items-center">
        <div class="relative mb-4" style="width:100px;height:100px">
          <img src="/ObolLogo_no_rings.svg" alt="OBOL Logo"
            class="absolute inset-0 w-full h-full object-contain pointer-events-none select-none"
            style="transform: scale(0.70);" />
          <svg class="absolute inset-0 w-full h-full obol-spinner" viewBox="0 0 100 100" aria-hidden="true">
            <defs>
              <linearGradient id="obol-gold" x1="0" y1="0" x2="1" y2="1">
                <stop offset="0%" stop-color="#F6D773" />
                <stop offset="60%" stop-color="#E6B422" />
                <stop offset="100%" stop-color="#B37E00" />
              </linearGradient>
            </defs>
            <g class="spin-cw">
              <circle cx="50" cy="50" r="47" fill="none" stroke="url(#obol-gold)" stroke-width="4"
                stroke-linecap="round" pathLength="100" stroke-dasharray="100 22" stroke-dashoffset="25" />
            </g>
            <g class="spin-ccw">
              <circle cx="50" cy="50" r="40" fill="none" stroke="url(#obol-gold)" stroke-width="3"
                stroke-linecap="round" opacity="0.95" pathLength="100" stroke-dasharray="100 22"
                stroke-dashoffset="25" />
            </g>
          </svg>
        </div>
        <h3 class="text-lg font-medium mb-2">Processing</h3>
        <p class="text-muted-foreground text-center">{{ loadingMessage }}</p>
      </div>
    </div>
  </div>

  <!-- Success -->
  <div v-if="!blockModals && showSuccess" class="fixed inset-0 bg-black/70 flex items-center justify-center z-50">
    <div class="bg-surface rounded-lg p-6 shadow-xl max-w-md w-full border border-slate-700">
      <div class="flex flex-col items-center">
        <div
          class="rounded-full h-16 w-16 bg-emerald-900/30 flex items-center justify-center mb-4 border border-emerald-700">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-10 w-10 text-emerald-400" fill="none" viewBox="0 0 24 24"
            stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7" />
          </svg>
        </div>
        <h3 class="text-lg font-medium mb-2">Success!</h3>
        <p class="text-muted-foreground text-center mb-4">{{ successMessage }}</p>
        <button @click="closeSuccessModal"
          class="px-4 py-2 bg-emerald-600 text-white rounded-md hover:bg-emerald-500 focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:ring-offset-2 focus:ring-offset-surface transition-all duration-200">
          Close
        </button>
      </div>
    </div>
  </div>

  <!-- Error -->
  <div v-if="showError" class="fixed inset-0 bg-black/70 flex items-center justify-center z-50">
    <div class="bg-surface rounded-lg p-6 shadow-xl max-w-md w-full border border-slate-700">
      <div class="flex flex-col items-center">
        <div class="rounded-full h-16 w-16 bg-red-900/30 flex items-center justify-center mb-4 border border-red-700">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-10 w-10 text-red-400" fill="none" viewBox="0 0 24 24"
            stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
          </svg>
        </div>
        <h3 class="text-lg font-medium mb-2">Error</h3>
        <p class="text-muted-foreground text-center mb-4">{{ errorMessage }}</p>
        <button @click="closeErrorModal"
          class="px-4 py-2 bg-red-600 text-white rounded-md hover:bg-red-500 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2 focus:ring-offset-surface transition-all duration-200">
          Close
        </button>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, onMounted, onUnmounted, reactive, computed, watch, nextTick } from 'vue'
import { BrowserProvider, Eip1193Provider } from 'ethers'
import { ethers } from 'ethers'

const SDK_URL = "https://cdn.zama.ai/relayer-sdk-js/0.2.0/relayer-sdk-js.js"
let initSDK: any
let createInstance: any
let SepoliaConfig: any
let fhevmInstance: any

import NETWORK_ENV from "../Network.json"
import OBOL_ENV from "../OBOL.json"
import ERC7984_ABI from "../contracts/ConfidentialToken.json"
import CONF_LEND_ABI from "../contracts/ConfLendMarket.json"
import ORACLE_ABI from "../contracts/ObolPriceOracle.json"

type Tab = { id: string; name: string }
type MarketKey = 'EURtoUSD' | 'USDtoEUR'

const REQUIRED_NETWORK = {
  chainId: NETWORK_ENV.public.CHAIN_ID,
  chainName: NETWORK_ENV.public.CHAIN_NAME || NETWORK_ENV.public.CHAIN_NAME,
  nativeCurrency: {
    name: NETWORK_ENV.public.CURRENCY_NAME,
    symbol: NETWORK_ENV.public.CURRENCY_SYMBOL,
    decimals: 18
  },
  rpcUrl: NETWORK_ENV.public.RPC_URL,
  blockExplorerUrl: NETWORK_ENV.public.EXPLORER_URL
}
function toHexChainId(v: any): string {
  if (typeof v === 'number') return `0x${v.toString(16)}`.toLowerCase()
  if (typeof v === 'string') {
    const s = v.trim().toLowerCase()
    if (s.startsWith('0x')) return s
    const n = Number(s)
    return Number.isFinite(n) ? `0x${n.toString(16)}`.toLowerCase() : ''
  }
  return ''
}
function parseChainIdToNumber(v: unknown): number | null {
  try {
    if (typeof v === 'number') return v
    if (typeof v === 'bigint') return Number(v)
    if (typeof v === 'string') {
      const s = v.trim().toLowerCase()
      if (s.startsWith('0x')) return parseInt(s, 16)
      const n = Number(s)
      return Number.isFinite(n) ? n : null
    }
  } catch { }
  return null
}
const REQUIRED_CHAIN_ID_HEX = toHexChainId(REQUIRED_NETWORK.chainId)
const REQUIRED_CHAIN_ID_NUM = parseChainIdToNumber(REQUIRED_NETWORK.chainId)!

const tabs: Tab[] = [
  { id: 'market', name: 'Market' },
  { id: 'portfolio', name: 'Portfolio' },
  { id: 'collateral', name: 'Collateral' },
  { id: 'borrow', name: 'Borrow' },
  { id: 'operators', name: 'Operators' },
  { id: 'tokens', name: 'Tokens' },
  { id: 'liquidation', name: 'Liquidation' },
  { id: 'supply', name: 'Supply' },
  { id: 'oracle', name: 'Oracle' },
]
const activeTab = ref<string>('market')

const isMetaMaskInstalled = ref(false)
const isConnected = ref(false)
const account = ref('')
const chainIdHex = ref('')
const chainIdNum = ref<number | null>(null)
const provider = (typeof window !== 'undefined' && (window.ethereum as Eip1193Provider))
  ? new BrowserProvider(window.ethereum as Eip1193Provider)
  : null

const isWrongNetwork = computed(() =>
  isMetaMaskInstalled.value &&
  chainIdNum.value !== null &&
  chainIdNum.value !== REQUIRED_CHAIN_ID_NUM
)

const loadingMessage = ref("Please wait…")
const showLoading = ref(false)
const showSuccess = ref(false)
const successMessage = ref('')
const showError = ref(false)
const errorMessage = ref('')
const copiedText = ref('')
let copyTimer: number | undefined;

function showSuccessModal(message: string) { if (blockModals.value) return; successMessage.value = message; showSuccess.value = true; setTimeout(closeSuccessModal, 3000) }
function closeSuccessModal() { showSuccess.value = false; successMessage.value = '' }
function showErrorModal(message: string) { errorMessage.value = message; showError.value = true; setTimeout(closeErrorModal, 4500) }
function closeErrorModal() { showError.value = false; errorMessage.value = '' }
const truncateAddress = (address: string): string => !address ? '' : `${address.slice(0, 6)}...${address.slice(-4)}`
function isCopied(v?: string) { if (!v || !copiedText.value) return false; return copiedText.value.toLowerCase() === v.toLowerCase() }
async function copyToClipboard(text: string) {
  try {
    if (navigator?.clipboard?.writeText) { await navigator.clipboard.writeText(text) }
    else {
      const ta = document.createElement('textarea'); ta.value = text; ta.style.position = 'fixed'; ta.style.left = '-9999px'
      document.body.appendChild(ta); ta.select(); document.execCommand('copy'); document.body.removeChild(ta)
    }
    if (copyTimer) clearTimeout(copyTimer)
    copiedText.value = text
    copyTimer = window.setTimeout(() => (copiedText.value = ''), 1800)
  } catch { showErrorModal('Failed to copy.') }
}
const refreshPage = () => window.location.reload()
async function openLoading(message: string) { if (blockModals.value) return; loadingMessage.value = message; showLoading.value = true; await nextTick(); await new Promise(requestAnimationFrame) }
function closeLoading() { showLoading.value = false }

const DISC_KEY = "OBOL_DISCLAIMER_ACK_V1";
const showDisclaimer = ref(true);
const dontShowAgain = ref(false);
function closeDisclaimer() { showDisclaimer.value = false }
function confirmDisclaimer() {
  if (dontShowAgain.value) { try { localStorage.setItem(DISC_KEY, "1") } catch { } }
  showDisclaimer.value = false
  if (isConnected.value && !isWrongNetwork.value) { initAfterConnect({ silent: true }).catch(() => { }) }
}

const tokenFactor = 1_000_000n
function scale6(n: number): bigint { return BigInt(Math.trunc(Number(n) * 1_000_000)) }
function from6(n6?: bigint | number | string): string {
  if (n6 === undefined || n6 === null) return '-'
  const b = BigInt(n6 as any)
  const int = b / tokenFactor
  const frac = (b % tokenFactor).toString().padStart(6, '0')
  return `${int}.${frac}`.replace(/\.?0+$/, '')
}
const format6 = (v?: bigint | number | string) => v == null ? '-' : from6(v)
const formatAPR = (apr6?: bigint | number | string) => {
  if (apr6 == null) return '-'
  const b = Number(BigInt(apr6 as any))
  const pct = (b / 1_000_000) * 100
  return `${pct.toFixed(2)}% / yr`
}

function toUnixSeconds(v: unknown): number {
  const toNum = (x: unknown): number => {
    if (x === null || x === undefined) return NaN
    if (typeof x === 'number') return x
    if (typeof x === 'bigint') return Number(x)
    if (typeof x === 'string') {
      const n = Number(x)
      return Number.isFinite(n) ? n : NaN
    }
    const anyV = x as any
    if (typeof anyV?.toString === 'function') {
      const n = Number(anyV.toString())
      return Number.isFinite(n) ? n : NaN
    }
    return NaN
  }

  const raw = toNum(v)
  if (!Number.isFinite(raw)) return NaN

  return raw >= 1_000_000_000_000 ? Math.floor(raw / 1000) : raw
}

function fmtTsUTC(ts: unknown, opts?: Intl.DateTimeFormatOptions): string {
  const sec = toUnixSeconds(ts)
  if (!Number.isFinite(sec) || sec <= 0) return '-'
  const d = new Date(sec * 1000)
  return new Intl.DateTimeFormat(undefined, {
    dateStyle: 'medium',
    timeStyle: 'short',
    hour12: false,
    timeZone: 'UTC',
    ...opts,
  }).format(d)
}

function fmtTsLocal(ts: unknown, opts?: Intl.DateTimeFormatOptions): string {
  const sec = toUnixSeconds(ts)
  if (!Number.isFinite(sec) || sec <= 0) return '-'
  const d = new Date(sec * 1000)
  return new Intl.DateTimeFormat(undefined, {
    dateStyle: 'medium',
    timeStyle: 'short',
    hour12: false,
    ...opts,
  }).format(d)
}

const selectedMarket = ref<MarketKey>('EURtoUSD')
const selectedMarketLabel = computed(() => selectedMarket.value === 'EURtoUSD' ? 'EUR → USD' : 'USD → EUR')

const markets = {
  EURtoUSD: String(OBOL_ENV.MARKET_EURtoUSD || ''),
  USDtoEUR: String(OBOL_ENV.MARKET_USDtoEUR || '')
}

const current = reactive({
  marketAddress: '',
  collat: '',
  debt: '',
  oracle: ''
})
const tokenSymbols = reactive({ collat: '--', debt: '--' })
const tokenBalances = reactive({ collat: '-', debt: '-' })

const scalars = reactive({
  price6: 0n as bigint,
  seizePerUnit6: 0n as bigint,
  borrowIndex6: 0n as bigint,
  supplyIndex6: 0n as bigint,
  borrowApr6: 0n as bigint,
  supplyApr6: 0n as bigint,
  lastAccrualTs: 0,
  directionLabel: '-',
  directionEnum: 0 as 0 | 1,
  lt6: 0n as bigint,
  hystBps: 0n as bigint,
})

const oracleMeta = reactive({
  eurPerUsd6: 0n as bigint,
  usdPerEur6: 0n as bigint,
  fresh: false,
  lastUpdateTs: 0,
  staleTtl: 0,
  msg: ''
})

const userClear = reactive({
  collat: '-',
  debt: '-',
  maxBorrow: '-',
})

const userPublic = reactive({
  A: 0n as bigint,
  B: 0n as bigint,
  userBorrowIndex6: 0n as bigint
})

const userHealth = reactive({
  hfRaw6: 0n as bigint,
  hfNorm6: 0n as bigint,
  threshold6: 0n as bigint,
  liquidatable: false
})

const supply = reactive({ deposit: 0, withdraw: 0 })
const collat = reactive({ add: 0, remove: 0 })
const loan = reactive({ borrow: 0, repay: 0 })
const priceUnits = reactive({ left: '--', right: '--' })

const liq = reactive({
  // scan liquidatable slice
  off1: 0, lim1: 25,
  scanning: false,
  liquidatable: [] as Array<{ addr: string; hfRaw6: bigint; hfNorm6: bigint; liquidatable: boolean }>,

  // browse active range
  off2: 0, lim2: 25,
  browsing: false,
  active: [] as Array<{ addr: string; hfRaw6: bigint; hfNorm6: bigint; liquidatable: boolean }>,

  // check one address
  checkAddr: '',
  checking: false,
  check: null as null | { addr: string; hfRaw6: bigint; hfNorm6: bigint; liquidatable: boolean },

  // perform liquidation
  target: '',
  repay: 0,
  pendingTx: false,

  // claim seized
  claimUser: '',
  pendingClaim: false,
})

const pending = reactive({
  metamask: false,
  refreshMarket: false,
  operator: false,
  pos: false,
  supply: false,
  collat: false,
  borrow: false,
  oracle: false,
  tokens: false,
})
const suppressLoading = ref(false);
const blockModals = computed(() => showDisclaimer.value || suppressLoading.value)
const nullHandle = "0x0000000000000000000000000000000000000000000000000000000000000000";

async function ensureConnectedState() {
  const eth = (typeof window !== 'undefined') ? (window as any).ethereum : undefined
  if (!eth) { isMetaMaskInstalled.value = false; isConnected.value = false; account.value = ''; chainIdHex.value = ''; chainIdNum.value = null; return }
  isMetaMaskInstalled.value = true
  try { const cid = await eth.request({ method: 'eth_chainId' }); chainIdHex.value = String(cid).toLowerCase(); chainIdNum.value = parseChainIdToNumber(cid) } catch { chainIdHex.value = ''; chainIdNum.value = null }
  try {
    const accounts = await eth.request({ method: 'eth_accounts' })
    if (Array.isArray(accounts) && accounts.length > 0) { account.value = accounts[0]; isConnected.value = true }
    else { account.value = ''; isConnected.value = false }
  } catch { }
}
let onAccountsChanged: ((accs: string[]) => void) | null = null
let onChainChanged: ((newId: string) => void) | null = null
let onConnect: ((info: any) => void) | null = null
let onDisconnect: ((err: any) => void) | null = null

async function checkMetaMaskConnection() {
  await ensureConnectedState()
  const eth = (typeof window !== 'undefined') ? (window as any).ethereum : undefined
  if (!eth) return
  if (onAccountsChanged) eth.removeListener?.('accountsChanged', onAccountsChanged)
  if (onChainChanged) eth.removeListener?.('chainChanged', onChainChanged)
  if (onConnect) eth.removeListener?.('connect', onConnect)
  if (onDisconnect) eth.removeListener?.('disconnect', onDisconnect)

  onAccountsChanged = async (accs: string[]) => {
    if (!Array.isArray(accs) || accs.length === 0) { isConnected.value = false; account.value = '' }
    else { isConnected.value = true; account.value = accs[0] }
    if (isConnected.value && !chainIdNum.value) {
      try { const cid = await eth.request({ method: 'eth_chainId' }); chainIdHex.value = String(cid).toLowerCase(); chainIdNum.value = parseChainIdToNumber(cid) } catch { }
    }
    if (!showDisclaimer.value && isConnected.value && !isWrongNetwork.value) {
      await loadMarket().catch(() => { })
    }
  }
  onChainChanged = async (newId: string) => {
    chainIdHex.value = String(newId).toLowerCase()
    chainIdNum.value = parseChainIdToNumber(newId)
    if (isConnected.value && chainIdNum.value === REQUIRED_CHAIN_ID_NUM) { await initAfterConnect().catch(() => { }) }
  }
  onConnect = async (_info: any) => { await ensureConnectedState(); if (isConnected.value && !isWrongNetwork.value) await initAfterConnect().catch(() => { }) }
  onDisconnect = (_err: any) => { isConnected.value = false }

  eth.on('accountsChanged', onAccountsChanged)
  eth.on('chainChanged', onChainChanged)
  eth.on('connect', onConnect)
  eth.on('disconnect', onDisconnect)
}

async function switchToRequiredNetwork() {
  if (!(window as any)?.ethereum) return showErrorModal('MetaMask not available.')
  try {
    await (window as any).ethereum.request({ method: 'wallet_switchEthereumChain', params: [{ chainId: REQUIRED_CHAIN_ID_HEX }] })
    chainIdHex.value = REQUIRED_CHAIN_ID_HEX
    chainIdNum.value = REQUIRED_CHAIN_ID_NUM
    showSuccessModal(`Switched to ${REQUIRED_NETWORK.chainName}.`)
    if (isConnected.value) await initAfterConnect()
  } catch (err: any) {
    if (err?.code === 4902) {
      try {
        await (window as any).ethereum.request({
          method: 'wallet_addEthereumChain',
          params: [{
            chainId: REQUIRED_CHAIN_ID_HEX, chainName: REQUIRED_NETWORK.chainName, nativeCurrency: REQUIRED_NETWORK.nativeCurrency,
            rpcUrls: [REQUIRED_NETWORK.rpcUrl], blockExplorerUrls: [REQUIRED_NETWORK.blockExplorerUrl],
          }],
        })
        chainIdHex.value = REQUIRED_CHAIN_ID_HEX
        chainIdNum.value = REQUIRED_CHAIN_ID_NUM
        showSuccessModal(`Added and switched to ${REQUIRED_NETWORK.chainName}.`)
        if (isConnected.value && !showDisclaimer.value) await initAfterConnect()
      } catch { showErrorModal('Please approve adding the network in MetaMask.') }
    } else if (err?.code === 4001) {
      showErrorModal('Please approve the network switch in MetaMask.')
    } else { showErrorModal('Unable to switch network.') }
  }
}

async function connectMetaMask() {
  if (!isMetaMaskInstalled.value) return showErrorModal('Please install MetaMask to use this application')
  pending.metamask = true
  try {
    const accounts = await (window as any).ethereum.request({ method: 'eth_requestAccounts' })
    account.value = accounts[0]; isConnected.value = true
    const cid = await (window as any).ethereum.request({ method: 'eth_chainId' })
    chainIdHex.value = String(cid).toLowerCase()
    chainIdNum.value = parseChainIdToNumber(cid)
    if (chainIdNum.value !== REQUIRED_CHAIN_ID_NUM) { await switchToRequiredNetwork() }
    else {
      showSuccessModal(`Connected: ${truncateAddress(account.value)}`);
      if (!showDisclaimer.value) {
        await initAfterConnect()
      }
    }
  } catch (e) { console.error(e); showErrorModal('Failed to connect to MetaMask') }
  finally { pending.metamask = false }
}

async function bulkReencryption(handleContractPairs: { handle: any, contractAddress: string }[], contractAddresses: string[]) {
  const signer = provider ? await provider.getSigner() : null
  if (!signer) { showErrorModal('No wallet connected.'); return }
  const keypair = fhevmInstance.generateKeypair();
  const startTimeStamp = Math.floor(Date.now() / 1000).toString();
  const durationDays = "10";
  const eip712 = fhevmInstance.createEIP712(keypair.publicKey, contractAddresses, startTimeStamp, durationDays)
  const signature = await signer.signTypedData(
    eip712.domain,
    { UserDecryptRequestVerification: eip712.types.UserDecryptRequestVerification },
    eip712.message,
  )
  const result = await fhevmInstance.userDecrypt(
    handleContractPairs, keypair.privateKey, keypair.publicKey, signature.replace("0x", ""),
    contractAddresses, await signer.getAddress(), startTimeStamp, durationDays
  )
  const out: { [k: string]: any } = {}
  for (const p of handleContractPairs) out[p.handle] = result[p.handle];
  return out;
}

async function loadMarket() {
  if (!isConnected.value) return
  const addr = markets[selectedMarket.value]
  if (!addr || !/^0x[a-fA-F0-9]{40}$/.test(addr)) { showErrorModal('Market address missing or invalid in OBOL.json'); return }
  current.marketAddress = addr
  await refreshMarket()
}

function syncPriceUnits() {
  priceUnits.left = tokenSymbols.collat
  priceUnits.right = tokenSymbols.debt
}

const ceilDiv = (a: bigint, b: bigint) => (a + b - 1n) / b

async function refreshMarket() {
  if (!isConnected.value) return showErrorModal('Connect wallet first')
  pending.refreshMarket = true
  await openLoading('Reading market...')
  try {
    const signer = await provider!.getSigner()
    const mkt = new ethers.Contract(current.marketAddress, CONF_LEND_ABI.abi, signer)

    current.collat = await mkt.collatTokenAddress()
    current.debt = await mkt.debtTokenAddress()
    current.oracle = await mkt.oracleAddress()

    const coll = new ethers.Contract(current.collat, ERC7984_ABI.abi, signer)
    const debt = new ethers.Contract(current.debt, ERC7984_ABI.abi, signer)
    tokenSymbols.collat = await coll.symbol()
    tokenSymbols.debt = await debt.symbol()
    syncPriceUnits()

    scalars.seizePerUnit6 = BigInt(await mkt.liquidationSeizePerUnit6())
    scalars.borrowIndex6 = BigInt(await mkt.borrowIndex6())
    scalars.supplyIndex6 = BigInt(await mkt.supplyIndex6())
    scalars.borrowApr6 = BigInt(await mkt.borrowApr6())
    scalars.supplyApr6 = BigInt(await mkt.supplyApr6())
    scalars.lastAccrualTs = Number(await mkt.lastAccrualTs())
    scalars.lt6 = BigInt(await mkt.LT_collat6())
    scalars.hystBps = BigInt(await mkt.HYST_BPS())

    const dir = Number(await mkt.direction()) as 0 | 1
    scalars.directionEnum = dir
    scalars.directionLabel = dir === 0 ? 'EUR collateral / USD debt' : 'USD collateral / EUR debt'

    await readOracleAndRecompute()

    showSuccessModal('Market refreshed')
  } catch (e) { console.error(e); showErrorModal('Failed to read market') }
  finally { pending.refreshMarket = false; closeLoading() }
}

async function loadUserPos() {
  if (!isConnected.value) return showErrorModal('Connect wallet first')
  pending.pos = true
  await openLoading('Loading position...')
  try {
    const signer = await provider!.getSigner()
    const mkt = new ethers.Contract(current.marketAddress, CONF_LEND_ABI.abi, signer)
    const pos = await mkt.pos(await signer.getAddress())

    userPublic.A = BigInt(pos[2])
    userPublic.B = BigInt(pos[3])
    userPublic.userBorrowIndex6 = BigInt(pos[5])

    const handleContractPairs: { handle: any, contractAddress: string }[] = []
    if (pos.eCollat && String(pos.eCollat) !== nullHandle) {
      handleContractPairs.push({ handle: pos.eCollat, contractAddress: current.marketAddress })
    }
    if (pos.eDebt && String(pos.eDebt) !== nullHandle) {
      handleContractPairs.push({ handle: pos.eDebt, contractAddress: current.marketAddress })
    }
    if (pos.maxBorrow && String(pos.maxBorrow) !== nullHandle) {
      handleContractPairs.push({ handle: pos.maxBorrow, contractAddress: current.marketAddress })
    }
    const addresses = Array.from(new Set(handleContractPairs.map(x => x.contractAddress)))
    if (handleContractPairs.length) {
      const dec = await bulkReencryption(handleContractPairs, addresses)
      if (dec == null) throw new Error('Decryption failed')
      userClear.collat = format6(dec[pos.eCollat] ?? 0)
      userClear.debt = format6(dec[pos.eDebt] ?? 0)
      userClear.maxBorrow = format6(dec[pos.maxBorrow] ?? 0)
    } else {
      userClear.collat = '0'
      userClear.debt = '0'
      userClear.maxBorrow = '-'
    }
    recomputeHealth()
    showSuccessModal('Position loaded')
  } catch (e) { console.error(e); showErrorModal('Failed to load position') }
  finally { pending.pos = false; closeLoading() }
}

async function refreshFactors() {
  if (!isConnected.value) return showErrorModal('Connect wallet first')
  pending.pos = true
  await openLoading('Requesting factor refresh...')
  try {
    const signer = await provider!.getSigner()
    const mkt = new ethers.Contract(current.marketAddress, CONF_LEND_ABI.abi, signer)
    const tx = await mkt.refreshMarketFactors(await signer.getAddress())
    const rc = await tx.wait()
    if (!rc?.status) throw new Error('refreshMarketFactors failed')
    showSuccessModal('Factors refresh requested')
  } catch (e) { console.error(e); showErrorModal('Failed to refresh factors') }
  finally { pending.pos = false; closeLoading() }
}

async function computeMaxBorrow() {
  if (!isConnected.value) return showErrorModal('Connect wallet first')
  pending.pos = true
  await openLoading('Computing max borrow...')
  try {
    const signer = await provider!.getSigner()
    const mkt = new ethers.Contract(current.marketAddress, CONF_LEND_ABI.abi, signer)
    const tx = await mkt.maxBorrow()
    const rc = await tx.wait()
    if (!rc?.status) throw new Error('maxBorrow tx failed')

    // Re-read pos & decrypt maxBorrow
    const pos = await mkt.pos(await signer.getAddress())
    const dec = await bulkReencryption([{ handle: pos.maxBorrow, contractAddress: current.marketAddress }], [current.marketAddress])
    userClear.maxBorrow = format6(dec?.[pos.maxBorrow] ?? 0)

    showSuccessModal('Max borrow computed')
  } catch (e) { console.error(e); showErrorModal('Failed to compute max borrow') }
  finally { pending.pos = false; closeLoading() }
}

/** ===== Operators ===== **/
const OPERATOR_SECS: number = Number(OBOL_ENV.OPERATOR_DEADLINE_SECS ?? (24 * 60 * 60))
async function setOperatorOn(tokenAddr: string, operatorAddr: string) {
  if (!isConnected.value) return showErrorModal('Connect wallet first')
  pending.operator = true
  await openLoading('Granting operator...')
  try {
    const signer = await provider!.getSigner()
    const tok = new ethers.Contract(tokenAddr, ERC7984_ABI.abi, signer)
    const block = await signer.provider!.getBlock('latest'); if (!block) throw new Error('Block not found')
    const deadline = Number(block.timestamp) + OPERATOR_SECS
    const tx = await tok.setOperator(operatorAddr, deadline)
    const rc = await tx.wait()
    if (!rc?.status) throw new Error('setOperator failed')
    showSuccessModal('Operator granted')
  } catch (e) { console.error(e); showErrorModal('Failed to grant operator') }
  finally { pending.operator = false; closeLoading() }
}

async function depositDebt() {
  if (!isConnected.value) return showErrorModal('Connect wallet first')
  pending.supply = true
  await openLoading('Encrypting & depositing...')
  try {
    const signer = await provider!.getSigner()
    const mkt = new ethers.Contract(current.marketAddress, CONF_LEND_ABI.abi, signer)
    const amt6 = scale6(Number(supply.deposit) || 0)

    const clear = await fhevmInstance.createEncryptedInput(current.marketAddress, await signer.getAddress())
    clear.add64(amt6)
    const encrypted = await clear.encrypt()

    const tx = await mkt['depositDebtAsset(bytes32,bytes)'](encrypted.handles[0], encrypted.inputProof)
    const rc = await tx.wait(); if (!rc?.status) throw new Error('deposit failed')

    showSuccessModal('Deposit complete')
  } catch (e) { console.error(e); showErrorModal('Deposit failed') }
  finally { pending.supply = false; closeLoading() }
}

async function withdrawDebt() {
  if (!isConnected.value) return showErrorModal('Connect wallet first')
  pending.supply = true
  await openLoading('Encrypting & withdrawing...')
  try {
    const signer = await provider!.getSigner()
    const mkt = new ethers.Contract(current.marketAddress, CONF_LEND_ABI.abi, signer)
    const amt6 = scale6(Number(supply.withdraw) || 0)

    const clear = await fhevmInstance.createEncryptedInput(current.marketAddress, await signer.getAddress())
    clear.add64(amt6)
    const encrypted = await clear.encrypt()

    const tx = await mkt['withdrawDebtAsset(bytes32,bytes)'](encrypted.handles[0], encrypted.inputProof)
    const rc = await tx.wait(); if (!rc?.status) throw new Error('withdraw failed')

    showSuccessModal('Withdraw complete')
  } catch (e) { console.error(e); showErrorModal('Withdraw failed') }
  finally { pending.supply = false; closeLoading() }
}

async function addCollateral() {
  if (!isConnected.value) return showErrorModal('Connect wallet first')
  pending.collat = true
  await openLoading('Encrypting & adding collateral...')
  try {
    const signer = await provider!.getSigner()
    const mkt = new ethers.Contract(current.marketAddress, CONF_LEND_ABI.abi, signer)
    const amt6 = scale6(Number(collat.add) || 0)

    const clear = await fhevmInstance.createEncryptedInput(current.marketAddress, await signer.getAddress())
    clear.add64(amt6)
    const encrypted = await clear.encrypt()

    const tx = await mkt['addCollateral(bytes32,bytes)'](encrypted.handles[0], encrypted.inputProof)
    const rc = await tx.wait(); if (!rc?.status) throw new Error('addCollateral failed')

    showSuccessModal('Collateral added')
  } catch (e) { console.error(e); showErrorModal('Add collateral failed') }
  finally { pending.collat = false; closeLoading() }
}

async function removeCollateral() {
  if (!isConnected.value) return showErrorModal('Connect wallet first')
  pending.collat = true
  await openLoading('Encrypting & removing collateral...')
  try {
    const signer = await provider!.getSigner()
    const mkt = new ethers.Contract(current.marketAddress, CONF_LEND_ABI.abi, signer)
    const amt6 = scale6(Number(collat.remove) || 0)

    const clear = await fhevmInstance.createEncryptedInput(current.marketAddress, await signer.getAddress())
    clear.add64(amt6)
    const encrypted = await clear.encrypt()

    const tx = await mkt['removeCollateral(bytes32,bytes)'](encrypted.handles[0], encrypted.inputProof)
    const rc = await tx.wait(); if (!rc?.status) throw new Error('removeCollateral failed')

    showSuccessModal('Collateral removed (safe clamped)')
  } catch (e) { console.error(e); showErrorModal('Remove collateral failed') }
  finally { pending.collat = false; closeLoading() }
}

async function doBorrow() {
  if (!isConnected.value) return showErrorModal('Connect wallet first')
  pending.borrow = true
  await openLoading('Encrypting & borrowing...')
  try {
    const signer = await provider!.getSigner()
    const mkt = new ethers.Contract(current.marketAddress, CONF_LEND_ABI.abi, signer)
    const amt6 = scale6(Number(loan.borrow) || 0)

    const clear = await fhevmInstance.createEncryptedInput(current.marketAddress, await signer.getAddress())
    clear.add64(amt6)
    const encrypted = await clear.encrypt()

    const tx = await mkt['borrow(bytes32,bytes)'](encrypted.handles[0], encrypted.inputProof)
    const rc = await tx.wait(); if (!rc?.status) throw new Error('borrow failed')

    showSuccessModal('Borrow successful')
  } catch (e) { console.error(e); showErrorModal('Borrow failed') }
  finally { pending.borrow = false; closeLoading() }
}

async function doRepay() {
  if (!isConnected.value) return showErrorModal('Connect wallet first')
  pending.borrow = true
  await openLoading('Encrypting & repaying...')
  try {
    const signer = await provider!.getSigner()
    const mkt = new ethers.Contract(current.marketAddress, CONF_LEND_ABI.abi, signer)
    const amt6 = scale6(Number(loan.repay) || 0)

    const clear = await fhevmInstance.createEncryptedInput(current.marketAddress, await signer.getAddress())
    clear.add64(amt6)
    const encrypted = await clear.encrypt()

    const tx = await mkt['repay(bytes32,bytes)'](encrypted.handles[0], encrypted.inputProof)
    const rc = await tx.wait(); if (!rc?.status) throw new Error('repay failed')

    showSuccessModal('Repay successful')
  } catch (e) { console.error(e); showErrorModal('Repay failed') }
  finally { pending.borrow = false; closeLoading() }
}

async function readOracleAndRecompute() {
  try {
    const signer = await provider!.getSigner()
    const oracle = new ethers.Contract(current.oracle, ORACLE_ABI.abi, signer)

    let raw = 0n
    try { raw = BigInt(await oracle.price6()) } catch { raw = 0n }

    const inv = raw > 0n ? ceilDiv(1_000_000_000_000n, raw) : 0n

    oracleMeta.usdPerEur6 = raw
    oracleMeta.eurPerUsd6 = inv

    console.log("Ts :", await oracle.epoch(), await oracle.getAddress(), await oracle.isFresh());

    try { oracleMeta.fresh = Boolean(await oracle.isFresh()) } catch { oracleMeta.fresh = false }
    try { oracleMeta.lastUpdateTs = Number(await oracle.epoch()) } catch { }
    try { oracleMeta.staleTtl = Number(await oracle.staleTtl()) } catch { }



    const dir = scalars.directionEnum
    const priceFromContract = (dir === 0) ? inv : raw
    scalars.price6 = priceFromContract
    syncPriceUnits()
    recomputeHealth()
  } catch (e) {
    console.error(e)
    oracleMeta.msg = 'Failed to read oracle'
  }
}
async function loadLiquidatableSlice() {
  if (!isConnected.value) return showErrorModal('Connect wallet first')
  liq.scanning = true
  await openLoading('Scanning liquidatable positions…')
  try {
    const signer = await provider!.getSigner()
    const mkt = new ethers.Contract(current.marketAddress, CONF_LEND_ABI.abi, signer)
    const [addrs, flags]: [string[], boolean[]] = await mkt.getLiquidatableSlice(liq.off1, liq.lim1)

    const rows: Array<{ addr: string; hfRaw6: bigint; hfNorm6: bigint; liquidatable: boolean }> = []
    await Promise.all(addrs.map(async (addr: string, i: number) => {
      try {
        const p = await mkt.pos(addr)
        const A = BigInt(p[2]); const B = BigInt(p[3]); const userBorrowIndex6 = BigInt(p[5])
        const { hfRaw6, hfNorm6, liquidatable } = computeHFFor(A, B, userBorrowIndex6)
        rows.push({ addr, hfRaw6, hfNorm6, liquidatable: flags[i] && liquidatable })
      } catch { /* skip */ }
    }))
    // show only truly liquidatable
    liq.liquidatable = rows.filter(r => r.liquidatable)
    showSuccessModal('Scan complete')
  } catch (e) { console.error(e); showErrorModal('Failed to scan liquidatable slice') }
  finally { liq.scanning = false; closeLoading() }
}

async function loadActiveRange() {
  if (!isConnected.value) return showErrorModal('Connect wallet first')
  liq.browsing = true
  await openLoading('Loading active positions…')
  try {
    const signer = await provider!.getSigner()
    const mkt = new ethers.Contract(current.marketAddress, CONF_LEND_ABI.abi, signer)
    const addrs: string[] = await mkt.getActiveBorrowers(liq.off2, liq.lim2)
    const rows: Array<{ addr: string; hfRaw6: bigint; hfNorm6: bigint; liquidatable: boolean }> = []
    await Promise.all(addrs.map(async (addr: string) => {
      try {
        const [p, isLiq]: any = await Promise.all([mkt.pos(addr), mkt.isLiquidatablePublic(addr)])
        const A = BigInt(p[2]); const B = BigInt(p[3]); const userBorrowIndex6 = BigInt(p[5])
        const { hfRaw6, hfNorm6, liquidatable } = computeHFFor(A, B, userBorrowIndex6)
        rows.push({ addr, hfRaw6, hfNorm6, liquidatable: Boolean(isLiq) && liquidatable })
      } catch { /* skip */ }
    }))
    liq.active = rows
    showSuccessModal('Loaded active slice')
  } catch (e) { console.error(e); showErrorModal('Failed to load active slice') }
  finally { liq.browsing = false; closeLoading() }
}

async function checkAddressHF() {
  if (!isConnected.value) return showErrorModal('Connect wallet first')
  const addr = (liq.checkAddr || '').trim()
  if (!/^0x[a-fA-F0-9]{40}$/.test(addr)) return showErrorModal('Invalid address')
  liq.checking = true
  await openLoading('Checking address…')
  try {
    const signer = await provider!.getSigner()
    const mkt = new ethers.Contract(current.marketAddress, CONF_LEND_ABI.abi, signer)
    const [p, isLiq]: any = await Promise.all([mkt.pos(addr), mkt.isLiquidatablePublic(addr)])
    const A = BigInt(p[2]); const B = BigInt(p[3]); const userBorrowIndex6 = BigInt(p[5])
    const { hfRaw6, hfNorm6, liquidatable } = computeHFFor(A, B, userBorrowIndex6)
    liq.check = { addr, hfRaw6, hfNorm6, liquidatable: Boolean(isLiq) && liquidatable }
    showSuccessModal('Address checked')
  } catch (e) { console.error(e); showErrorModal('Failed to check address') }
  finally { liq.checking = false; closeLoading() }
}

async function performLiquidation() {
  if (!isConnected.value) return showErrorModal('Connect wallet first')
  const target = (liq.target || '').trim()
  if (!/^0x[a-fA-F0-9]{40}$/.test(target)) return showErrorModal('Invalid target address')
  const amt6 = scale6(Number(liq.repay) || 0)
  if (amt6 <= 0n) return showErrorModal('Enter a positive repay amount')

  liq.pendingTx = true
  await openLoading('Encrypting & liquidating…')
  try {
    const signer = await provider!.getSigner()
    const mkt = new ethers.Contract(current.marketAddress, CONF_LEND_ABI.abi, signer)

    const clear = await fhevmInstance.createEncryptedInput(current.marketAddress, await signer.getAddress())
    clear.add64(amt6)
    const encrypted = await clear.encrypt()

    const tx = await mkt['liquidate(address,bytes32,bytes)'](target, encrypted.handles[0], encrypted.inputProof)
    const rc = await tx.wait(); if (!rc?.status) throw new Error('liquidate tx failed')

    showSuccessModal('Liquidation queued (claim required)')
  } catch (e: any) {
    console.error(e)
    showErrorModal(e?.reason || 'Liquidation failed (check STALE_PRICE / HEALTHY)')
  } finally { liq.pendingTx = false; closeLoading() }
}

async function claimSeized() {
  if (!isConnected.value) return showErrorModal('Connect wallet first')
  const user = (liq.claimUser || '').trim()
  if (!/^0x[a-fA-F0-9]{40}$/.test(user)) return showErrorModal('Invalid victim address')
  liq.pendingClaim = true
  await openLoading('Claiming seized collateral…')
  try {
    const signer = await provider!.getSigner()
    const mkt = new ethers.Contract(current.marketAddress, CONF_LEND_ABI.abi, signer)
    const tx = await mkt.claimLiquidation(user)
    const rc = await tx.wait(); if (!rc?.status) throw new Error('claim failed')
    showSuccessModal('Claimed seized collateral')
  } catch (e) { console.error(e); showErrorModal('Claim failed') }
  finally { liq.pendingClaim = false; closeLoading() }
}


function computeHFFor(A: bigint, B: bigint, userBorrowIndex6: bigint) {
  const price6 = scalars.price6;
  const borrowIndex6 = BigInt(scalars.borrowIndex6 || 0n);
  const LT = BigInt(scalars.lt6 || 0n);
  const HYST_BPS = BigInt(scalars.hystBps || 0n);

  const userIdx = userBorrowIndex6 === 0n ? borrowIndex6 : userBorrowIndex6;
  const idxRatio6 = (userIdx === 0n) ? 1_000_000n : (borrowIndex6 * 1_000_000n) / userIdx;

  const rhs = (((B * price6) / 1_000_000n) * idxRatio6) / 1_000_000n;
  const hfRaw6 = rhs === 0n ? 2_000_000n : (A / rhs);
  const hfNorm6 = LT === 0n ? hfRaw6 : (hfRaw6 * 1_000_000n) / LT;

  const liq = A < (rhs * LT * (10_000n + HYST_BPS)) / 10_000n;
  return { hfRaw6, hfNorm6, liquidatable: liq };
}

function recomputeHealth() {
  const A = userPublic.A;
  const B = userPublic.B;

  if (A === 0n && B === 0n) {
    userHealth.hfRaw6 = 0n;
    userHealth.hfNorm6 = 0n;
    userHealth.threshold6 = 0n;
    userHealth.liquidatable = false;
    return;
  }

  const price6 = scalars.price6;
  const borrowIndex6 = BigInt(scalars.borrowIndex6 || 0n);
  const LT = BigInt(scalars.lt6 || 0n);
  const HYST_BPS = BigInt(scalars.hystBps || 0n);

  const userIdx = userPublic.userBorrowIndex6 === 0n
    ? borrowIndex6
    : userPublic.userBorrowIndex6;

  const idxRatio6 =
    userIdx === 0n
      ? 1_000_000n
      : (borrowIndex6 * 1_000_000n) / userIdx;

  const rhs = (((B * price6) / 1_000_000n) * idxRatio6) / 1_000_000n;
  const hfScaled6 = rhs === 0n ? 2_000_000n : A / rhs;
  const threshold6 = (LT * (10_000n + HYST_BPS)) / 10_000n;
  const hfNormalized6 = LT === 0n ? hfScaled6 : (hfScaled6 * 1_000_000n) / LT;
  const liquidatable = A < (rhs * LT * (10_000n + HYST_BPS)) / 10_000n;

  userHealth.hfRaw6 = hfScaled6;
  userHealth.hfNorm6 = hfNormalized6;
  userHealth.threshold6 = threshold6;
  userHealth.liquidatable = liquidatable;

  console.log(
    "HF_raw:", ethers.formatUnits(hfScaled6, 6),
    "| HF_norm:", ethers.formatUnits(hfNormalized6, 6),
    "| thr:", ethers.formatUnits(threshold6, 6),
    "| liq:", liquidatable
  );
}

async function decryptTokenBalances() {
  if (!isConnected.value) return showErrorModal('Connect wallet first')
  pending.tokens = true
  await openLoading('Fetching confidential balances…')
  try {
    const signer = provider ? await provider.getSigner() : null
    if (!signer) throw new Error('No wallet')

    const a = current.collat
    const b = current.debt
    if (!a || !b) throw new Error('Token addresses missing')

    const user = await signer.getAddress()
    const tColl = new ethers.Contract(a, ERC7984_ABI.abi, signer)
    const tDebt = new ethers.Contract(b, ERC7984_ABI.abi, signer)

    const hColl = await tColl.confidentialBalanceOf(user)
    const hDebt = await tDebt.confidentialBalanceOf(user)

    loadingMessage.value = 'Decrypting balances…'
    const handleContractPairs: { handle: string; contractAddress: string }[] = []
    if (hColl && hColl !== nullHandle) handleContractPairs.push({ handle: hColl, contractAddress: a })
    if (hDebt && hDebt !== nullHandle) handleContractPairs.push({ handle: hDebt, contractAddress: b })

    if (handleContractPairs.length === 0) {
      tokenBalances.collat = '0'
      tokenBalances.debt = '0'
      showSuccessModal('Balances updated!')
      return
    }

    const contractAddresses = Array.from(new Set(handleContractPairs.map(x => x.contractAddress)))
    const decrypted = await bulkReencryption(handleContractPairs, contractAddresses)
    if (!decrypted) throw new Error('Decryption failed')

    tokenBalances.collat = format6(decrypted[hColl] ?? 0)
    tokenBalances.debt = format6(decrypted[hDebt] ?? 0)

    showSuccessModal('Balances updated!')
  } catch (e) {
    console.error(e)
    showErrorModal('Failed to decrypt balances')
  } finally {
    pending.tokens = false
    closeLoading()
  }
}

async function claimTokenAirdrop(tokenAddr: string, label: string) {
  if (!isConnected.value) return showErrorModal('Connect wallet first')
  pending.tokens = true
  await openLoading(`Claiming ${label} airdrop...`)
  try {
    const signer = await provider!.getSigner()
    const tok = new ethers.Contract(tokenAddr, ERC7984_ABI.abi, signer)
    const tx = await tok.airDrop()
    const rc = await tx.wait()
    if (!rc?.status) throw new Error("Airdrop tx failed")
    showSuccessModal(`${label} airdrop claimed`)
  } catch (e) {
    console.error(e)
    showErrorModal(`Failed to claim ${label} airdrop`)
  } finally {
    pending.tokens = false
    closeLoading()
  }
}

async function initAfterConnect(opts?: { silent?: boolean }) {
  const wasSilent = suppressLoading.value
  if (opts?.silent) suppressLoading.value = true
  try {
    if (!fhevmInstance) {
      const sdk = await import(/* @vite-ignore */ SDK_URL)
      initSDK = sdk.initSDK; createInstance = sdk.createInstance; SepoliaConfig = sdk.SepoliaConfig
      await initSDK()
      const fhevmConfig = { ...SepoliaConfig, network: (window as any).ethereum }
      fhevmInstance = await createInstance(fhevmConfig)
    }
    await loadMarket()
    await loadUserPos().catch(() => { })
  } finally {
    suppressLoading.value = wasSilent
  }
}

watch([isConnected, chainIdNum, selectedMarket], async ([conn, cidNum]) => {
  if (conn && cidNum === REQUIRED_CHAIN_ID_NUM && !isWrongNetwork.value) {
    await loadMarket().catch(() => { })
  }
})

onMounted(async () => {
  let hide = false; try { hide = localStorage.getItem(DISC_KEY) === "1" } catch { }
  showDisclaimer.value = !hide
  await checkMetaMaskConnection()
  if (!showDisclaimer.value && isConnected.value && !isWrongNetwork.value) {
    await initAfterConnect({ silent: true })
  }
})
onUnmounted(() => {
  const eth = (typeof window !== 'undefined') ? (window as any).ethereum : undefined
  if (!eth) return
  if (onAccountsChanged) eth.removeListener?.('accountsChanged', onAccountsChanged)
  if (onChainChanged) eth.removeListener?.('chainChanged', onChainChanged)
  if (onConnect) eth.removeListener?.('connect', onConnect)
  if (onDisconnect) eth.removeListener?.('disconnect', onDisconnect)
})
</script>

<style>
input[type="number"]::-webkit-inner-spin-button,
input[type="number"]::-webkit-outer-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

input[type="number"] {
  -moz-appearance: textfield;
}

.obol-spinner .spin-cw {
  animation: obol-spin-cw 1.9s linear infinite;
  transform-origin: 50% 50%;
}

.obol-spinner .spin-ccw {
  animation: obol-spin-ccw 2.1s linear infinite;
  transform-origin: 50% 50%;
}

@keyframes obol-spin-cw {
  to {
    transform: rotate(360deg);
  }
}

@keyframes obol-spin-ccw {
  to {
    transform: rotate(-360deg);
  }
}
</style>
