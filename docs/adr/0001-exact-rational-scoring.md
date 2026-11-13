# Exact rational scoring

The engine represents win percentage, win score and the quality weight `k` as exact rational numbers, never floating-point, and parity tests require exactly equal scores. The Python tool compares percentages as exact fractions, so 6-2 and 3-1 tie; floating-point with a tolerance could create or destroy ties and change which tiebreakers run. We chose exactness over the simpler tolerance-based comparison.
