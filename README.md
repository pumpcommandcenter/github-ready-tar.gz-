# github-ready-tar.gz-
Refined, debugged and hardened . Integrations, implementations .
# Updated Base44 Enhancement Prompt (with Wallet + Jupiter)

Copy and paste this into your Base44 AI chat:

---

Upgrade my existing minimal "Church of Pump" Base44 app into a professional degen dashboard for the $PUMP token.

**Official Token Mint (CA):** `AgTSWsyrSymK1h35LPhncvtyb9sgViJrwdxAvXjWpump`

**Theme:** Cyberpunk / neon degen style (dark backgrounds, hot pink, electric cyan, purple gradients). Make it feel premium but fun.

**Required Features:**

1. **Wallet Connection**
   - Prominent "Connect Wallet" button in the top navigation.
   - Support Phantom, Backpack, and Solflare.
   - When connected, display shortened wallet address + Disconnect button.
   - Only show sensitive actions (like Withdraw Fees) when a wallet is connected.

2. **Live On-Chain Stats**
   - Total Transfers count (from TransferCounter PDA)
   - Last Transfer timestamp
   - Current 1% Tax status explanation

3. **Recent Activity Feed**
   - Display both `TransferEvent` and `TransferWithdrawal` events
   - Show event type, amount, relevant addresses, and timestamp
   - Color code transfers (cyan) vs withdrawals (pink)

4. **Tax & Treasury Section**
   - Clear explanation of the 1% tax mechanics (using Token-2022 TransferFee)
   - Button to trigger `withdraw_fees` (only for authorized wallets)

5. **Jupiter Swap Integration**
   - Add a "Swap on Jupiter" button that opens Jupiter aggregator in a new tab or modal, pre-filled with the $PUMP mint.
   - Jupiter URL example: `https://jup.ag/swap/USDC-AgTSWsyrSymK1h35LPhncvtyb9sgViJrwdxAvXjWpump`

**Technical Details:**
- Transfer Hook Program: `PumpTokenHook11111111111111111111111111111111`
- Counter PDA seeds: `["counter"]`
- Events: `TransferEvent`, `TransferWithdrawal`
- Mint: `AgTSWsyrSymK1h35LPhncvtyb9sgViJrwdxAvXjWpump`

Make the dashboard fully responsive, with good loading states, error handling, and a production-ready feel. Prepare it for custom domain on Base44 Pro tier.

After building, summarize the main sections you created and any manual steps needed (especially for wallet connection and Jupiter button).
deploy:
  resources:
    limits:
      memory: 256M
pump_tax_accumulated          # Current accumulated 1% tax (gauge)
pump_tax_last_check_timestamp # Last successful scrape time
pump_tax_check_interval_ms    # Configured interval
scrape_configs:
  - job_name: 'pump-tax-monitor'
    static_configs:
      - targets: ['localhost:3000']
    scrape_interval: 30s

# 1. Create .env file (optional but recommended)
cp systemd/pump-tax-monitor.env.example .env
# Edit .env and set DISCORD_WEBHOOK=...

# 2. Run
docker compose -f docker-compose.monitor.yml up -d --build

# 3. Check logs
docker logs -f pump-tax-monitor

# 4. Test endpoints
curl http://localhost:3000/health
curl http://localhost:3000/metrics

{
  "status": "ok",
  "last_check": "2026-06-09T...",
  "current_tax": "12458392",
  "uptime_seconds": 18432
}
/metrics target to Prometheus