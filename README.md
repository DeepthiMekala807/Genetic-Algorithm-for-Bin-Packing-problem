# Genetic-Algorithm-for-Bin-Packing-problem
import random
NUM_ITEMS = 3
ITEM_SIZES = [4, 8, 2]  
BIN_CAPACITY = 5
POPULATION_SIZE = 4
GENERATIONS = 5
CROSSOVER_RATE = 0.8
MUTATION_RATE = 0.1

def create_chromosome():
    return [random.randint(0, POPULATION_SIZE - 1) for _ in range(NUM_ITEMS)]


def calculate_fitness(chromosome):
    bins = {}
    for item, bin_idx in enumerate(chromosome):
        if bin_idx not in bins:
            bins[bin_idx] = 0
        bins[bin_idx] += ITEM_SIZES[item]
    num_bins = len(bins)
    overfill_penalty = sum(max(0, size - BIN_CAPACITY) for size in bins.values())
    return 1 / (num_bins + overfill_penalty + 1e-6)


def crossover(parent1, parent2):
    if random.random() < CROSSOVER_RATE:
        point = random.randint(1, NUM_ITEMS - 1)
        child1 = parent1[:point] + parent2[point:]
        child2 = parent2[:point] + parent1[point:]
        return child1, child2
    return parent1, parent2


def mutate(chromosome): 
    for i in range(NUM_ITEMS):
        if random.random() < MUTATION_RATE:
            chromosome[i] = random.randint(0, POPULATION_SIZE - 1)
    return chromosome

def genetic_algorithm():
    population = [create_chromosome() for _ in range(POPULATION_SIZE)]
    best_fitness = 0
    best_chromosome = None

    for generation in range(GENERATIONS):
        fitness_scores = [calculate_fitness(chromosome) for chromosome in population]
        best_in_generation = max(fitness_scores)
        if best_in_generation > best_fitness:
            best_fitness = best_in_generation
            best_chromosome = population[fitness_scores.index(best_in_generation)]

        new_population = []
        while len(new_population) < POPULATION_SIZE:
            parent1, parent2 = random.choices(population, weights=fitness_scores, k=2)
            child1, child2 = crossover(parent1, parent2)
            new_population.extend([mutate(child1), mutate(child2)])

        population = new_population[:POPULATION_SIZE]

        print(f"Generation {generation + 1}: Best Fitness = {best_fitness}")

    return best_chromosome, best_fitness


best_schedule, best_fitness = genetic_algorithm()
print("\nBest Schedule:", best_schedule)
print("Best Fitness:", best_fitness)
print("Number of Bins Used:", int(1 / best_fitness))
