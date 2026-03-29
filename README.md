# Tamasha
Franchise issue

---

## Key Features Implemented:

✅ **Agent Behavior:**
- Franchisee (risk-averse, financially stressed)
- Franchisor (protect brand, avoid precedent)
- Lawyers (risk analysis, recommendations)
- Observer (neutral market view)

✅ **Memory & Learning:**
- Trust score evolution based on interactions
- Proposal history tracking
- Adaptive behavior based on past outcomes

✅ **Negotiation Mechanics:**
- Proposal type variations (rescission, settlement, litigation)
- Counter-proposals with deadlines
- Acceptance/rejection logic

✅ **Financial Modeling:**
- Investment: $800k
- Royalty rates: 4% + 3%
- Settlement ranges: $100-250k
- Litigation cost estimates

✅ **Scenario Analysis:**
- 4 distinct scenarios with different stances
- Comparative metrics across outcomes
- Risk assessment and recommendations

---
Visualization Module for Franchise Dispute Simulation
Generates comprehensive charts and dashboards
"""

import matplotlib.pyplot as plt
import matplotlib.patches as mpatches
import numpy as np
from typing import Dict, List
import seaborn as sns
from datetime import datetime

# Set style
sns.set_style("whitegrid")
plt.rcParams['figure.figsize'] = (14, 10)


class SimulationVisualizer:
    """Generate visualizations from simulation data"""
    
    def __init__(self, results: Dict, model_data: Dict):
        self.results = results
        self.model_data = model_data
        self.scenario_colors = {
            'immediate_rescission': '#d62728',
            'negotiation': '#2ca02c',
            'settlement': '#ff7f0e',
            'litigation_escalation': '#9467bd'
        }
    
    def plot_scenario_comparison(self, metric: str, save_path: str = None):
        """Compare metric across scenarios"""
        fig, ax = plt.subplots(figsize=(12, 6))
        
        scenarios = list(self.results.keys())
        values = []
        
        for scenario in scenarios:
            if metric == 'rescission_probability':
                values.append(self.results[scenario]['legal']['rescission_probability'])
            elif metric == 'litigation_risk':
                values.append(self.results[scenario]['franchisor']['litigation_risk'])
            elif metric == 'expected_value':
                values.append(self.results[scenario]['financial_outcomes']['expected_value_litigation'])
            elif metric == 'trust_score':
                values.append(self.results[scenario]['franchisee']['final_trust'])
        
        colors = [self.scenario_colors[s] for s in scenarios]
        bars = ax.bar(scenarios, values, color=colors, alpha=0.8, edgecolor='black', linewidth=1.5)
        
        ax.set_ylabel(metric.replace('_', ' ').title(), fontsize=12, fontweight='bold')
        ax.set_title(f'{metric.replace("_", " ").title()} Across Scenarios', 
                     fontsize=14, fontweight='bold')
        ax.grid(axis='y', alpha=0.3)
        
        # Add value labels on bars
        for bar, value in zip(bars, values):
            height = bar.get_height()
            if metric == 'expected_value':
                label = f'${value:,.0f}'
            else:
                label = f'{value:.2f}'
            ax.text(bar.get_x() + bar.get_width()/2., height,
                   label, ha='center', va='bottom', fontsize=10, fontweight='bold')
        
        plt.xticks(rotation=45, ha='right')
        plt.tight_layout()
        
        if save_path:
            plt.savefig(save_path, dpi=300, bbox_inches='tight')
        plt.show()
    
    def plot_all_metrics_dashboard(self, save_path: str = None):
        """Create comprehensive dashboard comparing all key metrics"""
        fig = plt.figure(figsize=(16, 12))
        gs = fig.add_gridspec(3, 3, hspace=0.3, wspace=0.3)
        
        scenarios = list(self.results.keys())
        colors = [self.scenario_colors[s] for s in scenarios]
        
        # 1. Rescission Probability
        ax1 = fig.add_subplot(gs[0, 0])
        rescission_probs = [self.results[s]['legal']['rescission_probability'] for s in scenarios]
        ax1.bar(range(len(scenarios)), rescission_probs, color=colors, alpha=0.8, edgecolor='black')
        ax1.set_title('Rescission Probability', fontweight='bold')
        ax1.set_ylabel('Probability')
        ax1.set_xticks(range(len(scenarios)))
        ax1.set_xticklabels([s.replace('_', '\n') for s in scenarios], fontsize=8)
        ax1.set_ylim([0, 1])
        ax1.grid(axis='y', alpha=0.3)
        
        # 2. Expected Financial Value
        ax2 = fig.add_subplot(gs[0, 1])
        expected_values = [self.results[s]['financial_outcomes']['expected_value_litigation'] for s in scenarios]
        ax2.bar(range(len(scenarios)), expected_values, color=colors, alpha=0.8, edgecolor='black')
        ax2.set_title('Expected Litigation Value', fontweight='bold')
        ax2.set_ylabel('Value ($)')
        ax2.set_xticks(range(len(scenarios)))
        ax2.set_xticklabels([s.replace('_', '\n') for s in scenarios], fontsize=8)
        ax2.grid(axis='y', alpha=0.3)
        
        # 3. Franchisee Trust
        ax3 = fig.add_subplot(gs[0, 2])
        trust_scores = [self.results[s]['franchisee']['final_trust'] for s in scenarios]
        ax3.bar(range(len(scenarios)), trust_scores, color=colors, alpha=0.8, edgecolor='black')
        ax3.set_title('Franchisee Trust Score', fontweight='bold')
        ax3.set_ylabel('Trust (0-1)')
        ax3.set_xticks(range(len(scenarios)))
        ax3.set_xticklabels([s.replace('_', '\n') for s in scenarios], fontsize=8)
        ax3.set_ylim([0, 1])
        ax3.grid(axis='y', alpha=0.3)
        
        # 4. Franchisor Litigation Risk
        ax4 = fig.add_subplot(gs[1, 0])
        lit_risks = [self.results[s]['franchisor']['litigation_risk'] for s in scenarios]
        ax4.bar(range(len(scenarios)), lit_risks, color=colors, alpha=0.8, edgecolor='black')
        ax4.set_title('Franchisor Litigation Risk', fontweight='bold')
        ax4.set_ylabel('Risk Level')
        ax4.set_xticks(range(len(scenarios)))
        ax4.set_xticklabels([s.replace('_', '\n') for s in scenarios], fontsize=8)
        ax4.set_ylim([0, 1])
        ax4.grid(axis='y', alpha=0.3)
        
        # 5. Settlement Recommended
        ax5 = fig.add_subplot(gs[1, 1])
        settlements = [1 if self.results[s]['legal']['settlement_recommended'] else 0 for s in scenarios]
        ax5.bar(range(len(scenarios)), settlements, color=colors, alpha=0.8, edgecolor='black')
        ax5.set_title('Settlement Recommended', fontweight='bold')
        ax5.set_ylabel('Recommended (0=No, 1=Yes)')
        ax5.set_xticks(range(len(scenarios)))
        ax5.set_xticklabels([s.replace('_', '\n') for s in scenarios], fontsize=8)
        ax5.set_ylim([0, 1.2])
        ax5.grid(axis='y', alpha=0.3)
        
        # 6. Days to Critical
        ax6 = fig.add_subplot(gs[1, 2])
        days_critical = [self.results[s]['franchisee']['days_to_critical'] for s in scenarios]
        ax6.bar(range(len(scenarios)), days_critical, color=colors, alpha=0.8, edgecolor='black')
        ax6.set_title('Franchisee Days to Critical', fontweight='bold')
        ax6.set_ylabel('Days')
        ax6.set_xticks(range(len(scenarios)))
        ax6.set_xticklabels([s.replace('_', '\n') for s in scenarios], fontsize=8)
        ax6.grid(axis='y', alpha=0.3)
        
        # 7. Legal Cost vs Expected Recovery
        ax7 = fig.add_subplot(gs[2, 0])
        legal_costs = [self.results[s]['legal']['estimated_legal_cost'] for s in scenarios]
        expected_recovery = [self.results[s]['financial_outcomes']['rescission_payout'] * 
                            self.results[s]['legal']['rescission_probability'] for s in scenarios]
        x = np.arange(len(scenarios))
        width = 0.35
        ax7.bar(x - width/2, legal_costs, width, label='Legal Cost', alpha=0.8, edgecolor='black')
        ax7.bar(x + width/2, expected_recovery, width, label='Expected Recovery', alpha=0.8, edgecolor='black')
        ax7.set_title('Legal Cost vs Expected Recovery', fontweight='bold')
        ax7.set_ylabel('Amount ($)')
        ax7.set_xticks(x)
        ax7.set_xticklabels([s.replace('_', '\n') for s in scenarios], fontsize=8)
        ax7.legend()
        ax7.grid(axis='y', alpha=0.3)
        
        # 8. Risk Level Assessment
        ax8 = fig.add_subplot(gs[2, 1])
        risk_mapping = {'LOW': 1, 'MEDIUM': 2, 'HIGH': 3, 'CRITICAL': 4}
        risk_levels = [risk_mapping[self.results[s]['legal']['risk_level']] for s in scenarios]
        ax8.bar(range(len(scenarios)), risk_levels, color=colors, alpha=0.8, edgecolor='black')
        ax8.set_title('Overall Risk Assessment', fontweight='bold')
        ax8.set_ylabel('Risk Level')
        ax8.set_xticks(range(len(scenarios)))
        ax8.set_xticklabels([s.replace('_', '\n') for s in scenarios], fontsize=8)
        ax8.set_yticks([1, 2, 3, 4])
        ax8.set_yticklabels(['LOW', 'MEDIUM', 'HIGH', 'CRITICAL'])
        ax8.grid(axis='y', alpha=0.3)
        
        # 9. Settlement Range
        ax9 = fig.add_subplot(gs[2, 2])
        settlement_low = [self.results[s]['financial_outcomes']['settlement_range'][0] for s in scenarios]
        settlement_high = [self.results[s]['financial_outcomes']['settlement_range'][1] for s in scenarios]
        x = np.arange(len(scenarios))
        ax9.bar(x, settlement_high, label='Settlement Range', color=colors, alpha=0.8, edgecolor='black')
        ax9.scatter(x, settlement_low, color='black', s=100, zorder=5, marker='_', linewidths=3)
        ax9.set_title('Settlement Range', fontweight='bold')
        ax9.set_ylabel('Amount ($)')
        ax9.set_xticks(x)
        ax9.set_xticklabels([s.replace('_', '\n') for s in scenarios], fontsize=8)
        ax9.grid(axis='y', alpha=0.3)
        
        fig.suptitle('Franchise Dispute Simulation - Comprehensive Analysis Dashboard', 
                     fontsize=16, fontweight='bold', y=0.995)
        
        if save_path:
            plt.savefig(save_path, dpi=300, bbox_inches='tight')
        plt.show()
    
    def plot_outcome_ranges(self, save_path: str = None):
        """Visualize financial outcome ranges by scenario"""
        fig, ax = plt.subplots(figsize=(12, 6))
        
        scenarios = list(self.results.keys())
        settlement_ranges = [self.results[s]['financial_outcomes']['settlement_range'] for s in scenarios]
        
        # Extract low and high
        lows = [r[0] for r in settlement_ranges]
        highs = [r[1] for r in settlement_ranges]
        mids = [(l + h) / 2 for l, h in zip(lows, highs)]
        
        x = np.arange(len(scenarios))
        
        # Plot ranges as error bars
        errors = [np.array(mids) - np.array(lows), np.array(highs) - np.array(mids)]
        colors = [self.scenario_colors[s] for s in scenarios]
        
        ax.bar(x, mids, yerr=errors, color=colors, alpha=0.8, 
               capsize=10, error_kw={'elinewidth': 2, 'capthick': 2}, edgecolor='black', linewidth=1.5)
        
        ax.set_ylabel('Settlement Amount ($)', fontsize=12, fontweight='bold')
        ax.set_title('Financial Outcome Ranges by Scenario', fontsize=14, fontweight='bold')
        ax.set_xticks(x)
        ax.set_xticklabels([s.replace('_', ' ').title() for s in scenarios], rotation=45, ha='right')
        ax.grid(axis='y', alpha=0.3)
        
        if save_path:
            plt.savefig(save_path, dpi=300, bbox_inches='tight')
        plt.show()
    
    def plot_time_to_resolution(self, time_series_data: Dict, save_path: str = None):
        """Plot time-to-resolution for each scenario"""
        fig, ax = plt.subplots(figsize=(12, 6))
        
        for scenario in self.results.keys():
            if scenario in time_series_data:
                steps = list(range(len(time_series_data[scenario])))
                ax.plot(steps, time_series_data[scenario], 
                       marker='o', label=scenario.replace('_', ' ').title(),
                       color=self.scenario_colors[scenario], linewidth=2)
        
        ax.set_xlabel('Simulation Steps', fontsize=12, fontweight='bold')
        ax.set_ylabel('Metric Value', fontsize=12, fontweight='bold')
        ax.set_title('Negotiation Progress Over Time', fontsize=14, fontweight='bold')
        ax.legend(loc='best')
        ax.grid(True, alpha=0.3)
        
        if save_path:
            plt.savefig(save_path, dpi=300, bbox_inches='tight')
        plt.show()
    
    def plot_risk_matrix(self, save_path: str = None):
        """Plot risk vs reward matrix"""
        fig, ax = plt.subplots(figsize=(10, 8))
        
        scenarios = list(self.results.keys())
        
        # X-axis: Rescission probability (risk)
        # Y-axis: Expected value (reward)
        
        for scenario in scenarios:
            risk = self.results[scenario]['legal']['rescission_probability']
            reward = self.results[scenario]['financial_outcomes']['expected_value_litigation']
            
            ax.scatter(risk, reward, s=500, alpha=0.7, 
                      color=self.scenario_colors[scenario],
                      edgecolors='black', linewidth=2,
                      label=scenario.replace('_', ' ').title())
        
        ax.set_xlabel('Risk (Rescission Probability)', fontsize=12, fontweight='bold')
        ax.set_ylabel('Reward (Expected Value $)', fontsize=12, fontweight='bold')
        ax.set_title('Risk-Reward Matrix by Scenario', fontsize=14, fontweight='bold')
        ax.legend(loc='best', fontsize=10)
        ax.grid(True, alpha=0.3)
        
        # Add quadrant lines
        ax.axhline(y=0, color='gray', linestyle='--', alpha=0.5)
        ax.axvline(x=0.5, color='gray', linestyle='--', alpha=0.5)
        
        # Add annotations
        ax.text(0.25, ax.get_ylim()[1] * 0.9, 'Low Risk\nLow Return', 
               ha='center', fontsize=9, style='italic', alpha=0.7)
        ax.text(0.75, ax.get_ylim()[1] * 0.9, 'High Risk\nHigh Return', 
               ha='center', fontsize=9, style='italic', alpha=0.7)
        
        if save_path:
            plt.savefig(save_path, dpi=300, bbox_inches='tight')
        plt.show()


if __name__ == "__main__":
    # Example usage (populated by main simulation)
    print("Visualization module ready. Import SimulationVisualizer to use.")


- Add more sophisticated agent learning?

Let me know what aspect you'd like to expand!
