# Module 04B: Quantum Stock Market Optimization

## DV-CV Quantum Computing for Equity Markets

**Part of Universal Rebalancing Theory by Mardochée JOSEPH**

---

## Learning Objectives

By the end of this chapter, you will:
- Apply quantum algorithms to equity portfolio optimization
- Implement sector rotation using QAOA
- Use CV states for earnings volatility modeling
- Build tax-loss harvesting quantum systems

---

## 4B.1 Stock Market Quantum Characteristics

### Equity-Specific Optimization Challenges

```
┌─────────────────────────────────────────────────────────────┐
│           STOCK MARKET QUANTUM OPPORTUNITIES                 │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  SECTOR ALLOCATION                                          │
│  • 11 GICS sectors, 100s of industries                     │
│  • Combinatorial optimization problem                       │
│  • QAOA for sector weights                                 │
│                                                             │
│  FACTOR EXPOSURE                                            │
│  • Value, Growth, Momentum, Quality, Size                  │
│  • Multi-factor portfolio construction                      │
│  • Quantum for factor timing                               │
│                                                             │
│  EARNINGS EVENTS                                            │
│  • Quarterly earnings = volatility spikes                  │
│  • CV squeezed states for pre/post earnings               │
│  • Uncertainty modeling                                    │
│                                                             │
│  TAX OPTIMIZATION                                           │
│  • Tax-loss harvesting opportunities                       │
│  • Wash sale rule compliance                               │
│  • Grover search for optimal harvests                      │
│                                                             │
│  MULTI-BROKER EXECUTION                                     │
│  • Route orders across brokers                             │
│  • Minimize market impact                                  │
│  • Quantum routing algorithms                              │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Stock-Specific URT Variables

```
Extended URT for Stocks:

E(R_stock,t) = Σᵦ [Fundamental_Value_β,t × Market_Sentiment_β,t × Execution_Quality_β]
             + Factor_Premium_t × Factor_Exposure_β

Risk(Stock,t) = √(Market_Risk_t² + Sector_Risk_t² + Idiosyncratic_Risk_t²
                + Earnings_Risk_t² + Liquidity_Risk_t²)

Cost(Stock,t) = Σᵦ [Commission_β,t + Bid_Ask_Spread_β,t + Market_Impact_β,t
                  + Tax_Cost_β,t - Tax_Benefit_β,t]

Constraints:
• Sector_Exposure(s) ≤ 25% for any sector s
• Single_Stock_Weight ≤ 5%
• Factor_Tilt within bounds
• Tax_Efficiency maximized
```

---

## 4B.2 Quantum Hamiltonian for Stocks

### Full Stock Market Hamiltonian

```
Ĥ_stock = Ĥ_fundamental + Ĥ_sector + Ĥ_factor + Ĥ_tax + Ĥ_execution

Components:

Ĥ_fundamental = -Σᵢ value_score_i × Ẑᵢ
                (Maximize fundamental value exposure)

Ĥ_sector = Σₛ penalty_s × (Σᵢ∈ₛ Ẑᵢ - target_s)²
           (Penalize sector over/under-weight)

Ĥ_factor = -Σf Σᵢ factor_loading_if × factor_premium_f × Ẑᵢ
           (Maximize factor exposure)

Ĥ_tax = -Σᵢ tax_benefit_i × Ẑᵢ + Σᵢ tax_cost_i × Ẑᵢ
        (Optimize tax efficiency)

Ĥ_execution = Σᵢ spread_i × |Ẑᵢ - Ẑᵢ,prev| + Σᵢ impact_i × Ẑᵢ²
              (Minimize execution costs)
```

### Sector Encoding

```
11 GICS Sectors encoded in quantum register:

|sector⟩ = |s₁s₂s₃s₄⟩  (4 qubits for 16 levels, 11 used)

Sector mapping:
|0000⟩ = Technology
|0001⟩ = Healthcare
|0010⟩ = Financials
|0011⟩ = Consumer Discretionary
|0100⟩ = Communication Services
|0101⟩ = Industrials
|0110⟩ = Consumer Staples
|0111⟩ = Energy
|1000⟩ = Utilities
|1001⟩ = Real Estate
|1010⟩ = Materials

Full portfolio state:
|Portfolio⟩ = Σₛ Σᵢ wₛᵢ |sector_s⟩ ⊗ |stock_i⟩
```

---

## 4B.3 QAOA for Sector Rotation

### Sector Rotation Problem

```
Given:
• Economic indicators (GDP, inflation, rates)
• Sector performance history
• Current market regime

Find: Optimal sector weights for next period

This is a constrained optimization:
• Σₛ wₛ = 1
• wₛ ≥ 0
• wₛ ≤ 0.25 (concentration limit)
```

### QAOA Implementation

```python
class QuantumSectorRotation:
    """
    QAOA-based sector rotation strategy
    """

    def __init__(self):
        self.sectors = [
            'Technology', 'Healthcare', 'Financials', 'Consumer_Disc',
            'Communication', 'Industrials', 'Consumer_Staples', 'Energy',
            'Utilities', 'Real_Estate', 'Materials'
        ]
        self.n_sectors = len(self.sectors)

    def build_sector_hamiltonian(self, expected_returns, covariance,
                                  economic_regime):
        """
        Build cost Hamiltonian for sector allocation

        H = -Σₛ R_s × Z_s + λ Σₛₜ Cov_st × Z_s × Z_t + penalties
        """
        H = {}

        # Return terms
        for s in range(self.n_sectors):
            # Adjust expected return based on economic regime
            regime_adjustment = self._regime_factor(s, economic_regime)
            H[(s,)] = -expected_returns[s] * regime_adjustment

        # Risk terms (covariance)
        for s in range(self.n_sectors):
            for t in range(s, self.n_sectors):
                H[(s, t)] = 0.5 * covariance[s, t]

        # Concentration penalty
        for s in range(self.n_sectors):
            H[(s, s)] = H.get((s, s), 0) + 0.1  # Penalize concentration

        return H

    def _regime_factor(self, sector_idx, regime):
        """
        Adjust sector returns based on economic regime

        Regimes: 'expansion', 'peak', 'contraction', 'trough'
        """
        regime_matrix = {
            'expansion': [1.2, 1.1, 1.1, 1.3, 1.1, 1.2, 0.8, 0.9, 0.7, 1.0, 1.1],
            'peak': [0.9, 1.1, 0.9, 0.8, 0.9, 0.8, 1.1, 1.2, 1.0, 0.9, 1.0],
            'contraction': [0.7, 1.2, 0.8, 0.6, 0.8, 0.7, 1.3, 0.8, 1.2, 0.9, 0.8],
            'trough': [1.1, 1.0, 1.2, 1.1, 1.0, 1.1, 1.0, 1.1, 1.0, 1.1, 1.2]
        }
        return regime_matrix.get(regime, [1.0] * 11)[sector_idx]

    def qaoa_circuit(self, H, p=4):
        """
        Build QAOA circuit for sector optimization
        """
        n_qubits = self.n_sectors * 4  # 4 bits per sector for 16 weight levels

        qc = QuantumCircuit(n_qubits)

        # Parameters
        gamma = [Parameter(f'γ_{i}') for i in range(p)]
        beta = [Parameter(f'β_{i}') for i in range(p)]

        # Initial superposition
        qc.h(range(n_qubits))

        for layer in range(p):
            # Cost unitary
            for term, coeff in H.items():
                if len(term) == 1:
                    s = term[0]
                    # Apply to all qubits encoding sector s
                    for q in range(s*4, (s+1)*4):
                        qc.rz(2 * gamma[layer] * coeff, q)
                elif len(term) == 2:
                    s1, s2 = term
                    # Cross-sector correlation
                    qc.cx(s1*4, s2*4)
                    qc.rz(2 * gamma[layer] * coeff, s2*4)
                    qc.cx(s1*4, s2*4)

            # Mixer
            for q in range(n_qubits):
                qc.rx(2 * beta[layer], q)

        return qc

    def optimize(self, market_data, regime='expansion'):
        """
        Run full sector rotation optimization
        """
        H = self.build_sector_hamiltonian(
            market_data['sector_returns'],
            market_data['sector_covariance'],
            regime
        )

        qc = self.qaoa_circuit(H, p=4)

        # Optimize parameters
        # ... (VQE-style optimization)

        # Measure and decode
        qc.measure_all()

        return self._decode_sector_weights(qc)

    def _decode_sector_weights(self, measurement):
        """
        Decode measurement to sector weights
        """
        weights = {}
        for s, sector in enumerate(self.sectors):
            bits = measurement[s*4:(s+1)*4]
            weight = int(bits, 2) / 16  # 0-100% in 6.25% steps
            weights[sector] = weight

        # Normalize
        total = sum(weights.values())
        if total > 0:
            weights = {k: v/total for k, v in weights.items()}

        return weights
```

---

## 4B.4 CV Quantum for Earnings Volatility

### Earnings Event Modeling

```
Pre-earnings: High uncertainty → Anti-squeezed state
Post-earnings: Resolved uncertainty → Price jump + reduced volatility

CV Model:
|pre_earnings⟩ = S(-r)|α⟩   (anti-squeezed coherent state)
|post_earnings⟩ = D(Δ)S(r')|α⟩  (displaced, potentially squeezed)

Where:
r = earnings uncertainty parameter
Δ = earnings surprise (price jump)
r' = post-earnings volatility adjustment
```

### CV Earnings Circuit

```python
def cv_earnings_model(stock_data, days_to_earnings):
    """
    Model stock behavior around earnings using CV quantum states
    """
    prog = sf.Program(2)  # 2 modes: price, implied_vol

    with prog.context as q:
        # Current price as coherent state
        current_price = stock_data['price']
        ops.Coherent(current_price / 100) | q[0]  # Normalize

        # Pre-earnings volatility expansion
        if days_to_earnings < 30:
            # Implied vol increases as earnings approach
            vol_expansion = 30 / max(days_to_earnings, 1)
            # Anti-squeezing = uncertainty expansion
            ops.Sgate(-np.log(vol_expansion)) | q[0]

            # Implied vol mode
            implied_vol = stock_data['implied_vol']
            ops.Coherent(implied_vol) | q[1]
            ops.Sgate(-np.log(vol_expansion) * 0.5) | q[1]

            # Correlate price and vol
            ops.S2gate(0.3) | (q[0], q[1])

        else:
            # Normal regime
            base_vol = stock_data['historical_vol']
            ops.Sgate(np.log(1 + base_vol)) | q[0]
            ops.Coherent(base_vol) | q[1]

        # Measurement
        ops.MeasureX | q[0]
        ops.MeasureX | q[1]

    return prog


def simulate_earnings_scenarios(stock, n_scenarios=1000):
    """
    Simulate stock price scenarios around earnings
    """
    scenarios = {
        'pre_earnings': [],
        'post_beat': [],
        'post_miss': [],
        'post_inline': []
    }

    # Pre-earnings distribution
    prog = cv_earnings_model(stock, days_to_earnings=5)
    eng = sf.Engine("gaussian")

    for _ in range(n_scenarios):
        result = eng.run(prog)
        price = result.samples[0][0] * 100
        scenarios['pre_earnings'].append(price)

    # Post-earnings scenarios
    for scenario_type, surprise in [('post_beat', 0.05),
                                     ('post_miss', -0.05),
                                     ('post_inline', 0.0)]:
        prog = sf.Program(1)
        with prog.context as q:
            ops.Coherent(stock['price'] * (1 + surprise) / 100) | q[0]
            ops.Sgate(0.3) | q[0]  # Vol crush after earnings
            ops.MeasureX | q[0]

        for _ in range(n_scenarios):
            result = eng.run(prog)
            price = result.samples[0][0] * 100
            scenarios[scenario_type].append(price)

    return scenarios


def earnings_risk_adjustment(portfolio, earnings_calendar):
    """
    Adjust portfolio risk based on upcoming earnings
    """
    adjustments = {}

    for stock, weight in portfolio.items():
        days_to_earnings = earnings_calendar.get(stock, 365)

        if days_to_earnings < 7:
            # High earnings risk - reduce weight or hedge
            risk_multiplier = 2.0
            adjustments[stock] = {
                'action': 'reduce' if weight > 0.03 else 'hold',
                'risk_multiplier': risk_multiplier,
                'suggested_weight': weight / risk_multiplier
            }
        elif days_to_earnings < 30:
            # Moderate earnings risk
            risk_multiplier = 1.5
            adjustments[stock] = {
                'action': 'monitor',
                'risk_multiplier': risk_multiplier,
                'suggested_weight': weight / risk_multiplier
            }
        else:
            adjustments[stock] = {
                'action': 'normal',
                'risk_multiplier': 1.0,
                'suggested_weight': weight
            }

    return adjustments
```

---

## 4B.5 Tax-Loss Harvesting with Grover Search

### Tax Optimization Problem

```
Given:
• Portfolio with gains and losses
• Tax rates (short-term, long-term)
• Wash sale constraints (30-day rule)
• Substantially identical security rules

Find: Optimal set of losses to harvest that:
• Maximizes tax benefit
• Maintains portfolio exposure
• Avoids wash sales
• Considers transaction costs
```

### Quantum Tax Optimizer

```python
class QuantumTaxHarvester:
    """
    Grover-based tax-loss harvesting optimization
    """

    def __init__(self, portfolio, tax_rates):
        self.portfolio = portfolio
        self.tax_rates = tax_rates
        self.n_positions = len(portfolio)

    def calculate_tax_benefit(self, harvest_set):
        """
        Calculate tax benefit from harvesting given positions
        """
        total_benefit = 0

        for pos_id in harvest_set:
            pos = self.portfolio[pos_id]

            if pos['gain_loss'] < 0:  # Loss position
                loss = abs(pos['gain_loss'])

                if pos['holding_period'] < 365:
                    # Short-term loss offsets short-term gains first
                    benefit = loss * self.tax_rates['short_term']
                else:
                    # Long-term loss
                    benefit = loss * self.tax_rates['long_term']

                # Subtract transaction costs
                benefit -= pos['transaction_cost']

                total_benefit += benefit

        return total_benefit

    def check_wash_sale(self, harvest_set, replacement_set, recent_trades):
        """
        Check for wash sale violations
        """
        for pos_id in harvest_set:
            pos = self.portfolio[pos_id]
            symbol = pos['symbol']

            # Check 30 days before and after
            for trade in recent_trades:
                if trade['symbol'] == symbol or self._substantially_identical(symbol, trade['symbol']):
                    days_diff = abs((trade['date'] - pos['sale_date']).days)
                    if days_diff <= 30:
                        return True  # Wash sale violation

        return False

    def _substantially_identical(self, sym1, sym2):
        """
        Check if two securities are substantially identical
        """
        # Same company
        if sym1.split('.')[0] == sym2.split('.')[0]:
            return True

        # Check if tracking same index
        # ... (implementation)

        return False

    def build_oracle(self, threshold_benefit):
        """
        Oracle marks harvest sets with benefit > threshold
        """
        n_qubits = self.n_positions

        oracle = QuantumCircuit(n_qubits + 1)

        # Oracle implementation:
        # 1. Compute tax benefit in superposition
        # 2. Compare with threshold
        # 3. Flip oracle qubit if benefit > threshold

        # Simplified: Use phase oracle
        # In practice, this requires arithmetic circuits

        return oracle

    def grover_harvest(self, min_benefit):
        """
        Find optimal harvest set using Grover search
        """
        n_qubits = self.n_positions

        qc = QuantumCircuit(n_qubits + 1, n_qubits)

        # Initialize
        qc.h(range(n_qubits))
        qc.x(n_qubits)
        qc.h(n_qubits)

        # Grover iterations
        n_iterations = int(np.pi / 4 * np.sqrt(2**n_qubits))

        for _ in range(n_iterations):
            # Oracle
            qc.compose(self.build_oracle(min_benefit), inplace=True)

            # Diffusion
            qc.h(range(n_qubits))
            qc.x(range(n_qubits))
            qc.h(n_qubits - 1)
            qc.mcx(list(range(n_qubits - 1)), n_qubits - 1)
            qc.h(n_qubits - 1)
            qc.x(range(n_qubits))
            qc.h(range(n_qubits))

        qc.measure(range(n_qubits), range(n_qubits))

        return qc

    def find_optimal_harvest(self):
        """
        Find optimal tax-loss harvesting strategy
        """
        # Binary search on benefit threshold
        low_benefit = 0
        high_benefit = sum(abs(p['gain_loss']) for p in self.portfolio
                         if p['gain_loss'] < 0) * self.tax_rates['short_term']

        best_harvest = None
        best_benefit = 0

        while high_benefit - low_benefit > 100:  # $100 precision
            mid_benefit = (low_benefit + high_benefit) / 2

            qc = self.grover_harvest(mid_benefit)

            # Run and check
            simulator = AerSimulator()
            result = simulator.run(qc, shots=100).result()
            counts = result.get_counts()

            # Check if valid harvest found
            for bitstring, count in counts.items():
                harvest_set = self._decode_harvest(bitstring)
                benefit = self.calculate_tax_benefit(harvest_set)

                if benefit > best_benefit:
                    if not self.check_wash_sale(harvest_set, [], []):
                        best_harvest = harvest_set
                        best_benefit = benefit

            if best_benefit >= mid_benefit:
                low_benefit = mid_benefit
            else:
                high_benefit = mid_benefit

        return best_harvest, best_benefit

    def _decode_harvest(self, bitstring):
        """
        Decode bitstring to harvest set
        """
        harvest = []
        for i, bit in enumerate(bitstring[::-1]):
            if bit == '1':
                harvest.append(i)
        return harvest


# Example usage
portfolio = [
    {'symbol': 'AAPL', 'gain_loss': -5000, 'holding_period': 400, 'transaction_cost': 10},
    {'symbol': 'GOOGL', 'gain_loss': 10000, 'holding_period': 200, 'transaction_cost': 10},
    {'symbol': 'MSFT', 'gain_loss': -3000, 'holding_period': 100, 'transaction_cost': 10},
    {'symbol': 'AMZN', 'gain_loss': -2000, 'holding_period': 500, 'transaction_cost': 10},
    {'symbol': 'META', 'gain_loss': 8000, 'holding_period': 300, 'transaction_cost': 10},
]

tax_rates = {'short_term': 0.37, 'long_term': 0.20}

harvester = QuantumTaxHarvester(portfolio, tax_rates)
best_harvest, benefit = harvester.find_optimal_harvest()
print(f"Optimal harvest: {best_harvest}")
print(f"Tax benefit: ${benefit:.2f}")
```

---

## 4B.6 Factor Investing with Quantum

### Multi-Factor Model

```
Factor returns:
R_i = α_i + Σ_f β_if × F_f + ε_i

Factors (Fama-French + extensions):
• Market (MKT): β to market
• Size (SMB): Small minus Big
• Value (HML): High minus Low book/market
• Momentum (MOM): Past winners minus losers
• Quality (QMJ): Quality minus Junk
• Low Volatility (LVOL): Low vol minus High vol

Quantum optimization: Find optimal factor tilts
```

### Quantum Factor Optimizer

```python
class QuantumFactorOptimizer:
    """
    Optimize multi-factor portfolio using quantum computing
    """

    def __init__(self, factors):
        self.factors = factors  # ['MKT', 'SMB', 'HML', 'MOM', 'QMJ', 'LVOL']
        self.n_factors = len(factors)

    def build_factor_hamiltonian(self, factor_returns, factor_covariance,
                                  factor_views):
        """
        Hamiltonian for factor tilt optimization

        Maximize expected factor returns subject to risk
        """
        H = {}

        # Factor return terms
        for f in range(self.n_factors):
            # Include views (Black-Litterman style)
            adjusted_return = factor_returns[f] * factor_views.get(self.factors[f], 1.0)
            H[(f,)] = -adjusted_return

        # Factor correlation risk
        for f1 in range(self.n_factors):
            for f2 in range(f1, self.n_factors):
                H[(f1, f2)] = factor_covariance[f1, f2]

        return H

    def qaoa_factor_tilts(self, H, p=3):
        """
        QAOA for optimal factor tilts

        Each factor gets 3 qubits (8 tilt levels: -0.4 to 0.4)
        """
        n_qubits = self.n_factors * 3

        qc = QuantumCircuit(n_qubits)

        gamma = [0.5] * p
        beta = [0.3] * p

        qc.h(range(n_qubits))

        for layer in range(p):
            # Cost
            for term, coeff in H.items():
                if len(term) == 1:
                    f = term[0]
                    for q in range(f*3, (f+1)*3):
                        qc.rz(2 * gamma[layer] * coeff, q)
                else:
                    f1, f2 = term
                    qc.cx(f1*3, f2*3)
                    qc.rz(2 * gamma[layer] * coeff, f2*3)
                    qc.cx(f1*3, f2*3)

            # Mixer
            for q in range(n_qubits):
                qc.rx(2 * beta[layer], q)

        return qc

    def decode_tilts(self, measurement):
        """
        Decode measurement to factor tilts
        """
        tilts = {}
        for f, factor in enumerate(self.factors):
            bits = measurement[f*3:(f+1)*3]
            # Map 0-7 to -0.4 to +0.4
            value = (int(bits, 2) - 4) / 10
            tilts[factor] = value
        return tilts


# Factor views (market expectations)
factor_views = {
    'MKT': 1.0,    # Neutral on market
    'SMB': 0.8,    # Slightly bearish on small caps
    'HML': 1.2,    # Bullish on value
    'MOM': 1.1,    # Slightly bullish on momentum
    'QMJ': 1.3,    # Bullish on quality
    'LVOL': 1.2    # Bullish on low vol
}

optimizer = QuantumFactorOptimizer(['MKT', 'SMB', 'HML', 'MOM', 'QMJ', 'LVOL'])
```

---

## 4B.7 Multi-Broker Execution Optimization

### Smart Order Routing

```python
class QuantumSmartRouter:
    """
    Quantum-optimized smart order routing across brokers
    """

    def __init__(self, brokers):
        self.brokers = brokers
        self.n_brokers = len(brokers)

    def route_order(self, order):
        """
        Find optimal order routing using Grover search
        """
        # Each broker can get 0-100% of order in 10% increments
        # Total combinations: 11^n_brokers

        n_qubits = self.n_brokers * 4  # 16 levels each

        qc = QuantumCircuit(n_qubits + 1, n_qubits)

        # Oracle marks routes with cost below threshold
        threshold = self._estimate_threshold(order)

        # Grover iterations
        qc.h(range(n_qubits))
        qc.x(n_qubits)
        qc.h(n_qubits)

        n_iter = int(np.pi/4 * np.sqrt(2**n_qubits / self._estimate_good_routes(order)))

        for _ in range(min(n_iter, 100)):
            # Oracle
            qc.compose(self._build_routing_oracle(order, threshold), inplace=True)

            # Diffusion
            qc.h(range(n_qubits))
            qc.x(range(n_qubits))
            qc.h(n_qubits - 1)
            qc.mcx(list(range(n_qubits - 1)), n_qubits - 1)
            qc.h(n_qubits - 1)
            qc.x(range(n_qubits))
            qc.h(range(n_qubits))

        qc.measure(range(n_qubits), range(n_qubits))

        return qc

    def _build_routing_oracle(self, order, threshold):
        """
        Build oracle for routing optimization
        """
        # Oracle marks allocations where:
        # Σ_b allocation_b = 100% AND
        # total_cost < threshold

        oracle = QuantumCircuit(self.n_brokers * 4 + 1)
        # ... implementation
        return oracle

    def _estimate_threshold(self, order):
        """Estimate cost threshold for Grover"""
        base_cost = order['size'] * 0.001  # 10 bps
        return base_cost

    def _estimate_good_routes(self, order):
        """Estimate number of good routing solutions"""
        return 2**(self.n_brokers * 4) * 0.1  # ~10% are good
```

---

## 4B.8 Complete Stock Quantum System

```python
class QuantumStockPortfolioManager:
    """
    Complete quantum stock portfolio management
    """

    def __init__(self, config):
        self.config = config

        # Initialize components
        self.sector_optimizer = QuantumSectorRotation()
        self.factor_optimizer = QuantumFactorOptimizer(config['factors'])
        self.tax_harvester = QuantumTaxHarvester([], config['tax_rates'])
        self.smart_router = QuantumSmartRouter(config['brokers'])

    def optimize_portfolio(self, market_data, current_holdings):
        """
        Full portfolio optimization
        """
        results = {}

        # 1. Sector allocation
        print("Optimizing sector allocation...")
        sector_weights = self.sector_optimizer.optimize(
            market_data,
            regime=market_data['economic_regime']
        )
        results['sector_weights'] = sector_weights

        # 2. Factor tilts
        print("Optimizing factor tilts...")
        factor_tilts = self._optimize_factors(market_data)
        results['factor_tilts'] = factor_tilts

        # 3. Stock selection within sectors
        print("Selecting stocks...")
        stock_weights = self._select_stocks(sector_weights, factor_tilts, market_data)
        results['stock_weights'] = stock_weights

        # 4. Tax-loss harvesting
        print("Checking tax harvesting opportunities...")
        self.tax_harvester.portfolio = current_holdings
        harvest, benefit = self.tax_harvester.find_optimal_harvest()
        results['tax_harvest'] = {'positions': harvest, 'benefit': benefit}

        # 5. Calculate trades
        trades = self._calculate_trades(stock_weights, current_holdings)

        # 6. Optimize execution
        print("Optimizing execution...")
        for trade in trades:
            trade['routing'] = self._optimize_routing(trade)

        results['trades'] = trades

        return results

    def _optimize_factors(self, market_data):
        """Optimize factor tilts"""
        H = self.factor_optimizer.build_factor_hamiltonian(
            market_data['factor_returns'],
            market_data['factor_covariance'],
            market_data.get('factor_views', {})
        )

        qc = self.factor_optimizer.qaoa_factor_tilts(H)
        # Run and decode...

        return {'MKT': 0.0, 'SMB': -0.1, 'HML': 0.2, 'MOM': 0.1, 'QMJ': 0.2, 'LVOL': 0.1}

    def _select_stocks(self, sector_weights, factor_tilts, market_data):
        """Select stocks within each sector"""
        stock_weights = {}

        for sector, sector_weight in sector_weights.items():
            sector_stocks = market_data['stocks_by_sector'][sector]

            # Score stocks by factor exposure
            for stock in sector_stocks:
                score = 0
                for factor, tilt in factor_tilts.items():
                    score += tilt * stock['factor_loadings'].get(factor, 0)

                # Combine with fundamental score
                total_score = score + stock['fundamental_score']
                stock_weights[stock['symbol']] = max(0, total_score * sector_weight / 10)

        # Normalize
        total = sum(stock_weights.values())
        if total > 0:
            stock_weights = {k: v/total for k, v in stock_weights.items()}

        return stock_weights

    def _calculate_trades(self, target, current):
        """Calculate required trades"""
        trades = []

        all_symbols = set(target.keys()) | set(current.keys())

        for symbol in all_symbols:
            target_weight = target.get(symbol, 0)
            current_weight = current.get(symbol, 0)
            delta = target_weight - current_weight

            if abs(delta) > 0.005:
                trades.append({
                    'symbol': symbol,
                    'side': 'buy' if delta > 0 else 'sell',
                    'size': abs(delta) * self.config['total_capital'],
                    'weight_change': delta
                })

        return trades

    def _optimize_routing(self, trade):
        """Optimize trade routing"""
        qc = self.smart_router.route_order(trade)
        # Run and decode...

        return {'broker_1': 0.5, 'broker_2': 0.3, 'broker_3': 0.2}
```

---

## 4B.9 Summary

### Key Stock Market Quantum Equations

```
Sector Rotation QAOA:
|ψ_sectors⟩ = QAOA(Ĥ_sector) where Ĥ_sector = Σₛ -Rₛ×Ẑₛ + λΣₛₜ Covₛₜ×ẐₛẐₜ

Earnings Volatility (CV):
|pre_earnings⟩ = S(-r_uncertainty)|price⟩
|post_earnings⟩ = D(surprise)S(r_crush)|price⟩

Tax Harvesting (Grover):
|optimal_harvest⟩ = Grover^k(Oracle_tax_benefit)|+⟩^n

Factor Tilts:
Ĥ_factor = -Σf premium_f × tilt_f + λ Σff' cov_ff' × tilt_f × tilt_f'
```

### Performance Summary

| Task | Classical | Quantum | Notes |
|------|-----------|---------|-------|
| Sector rotation | O(11!) | O(√11!) | All permutations |
| Tax harvesting | O(2^n) | O(√2^n) | Subset selection |
| Factor optimization | O(8^6) | O(8^3) | 6 factors, 8 levels |
| Multi-broker routing | O(B^10) | O(√B^10) | B brokers |

---

## Next Chapter

[Chapter 4C: Quantum Forex Optimization →](04c-forex-quantum.md)
