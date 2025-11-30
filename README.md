# Summative-assessment-analyticse
This is a repositiory used for my summative assessment analytics

from ortools.constraint_solver import pywrapcp, routing_enums_pb2
import math

# Locations (coordinates)
locations = [
    (51.731490, 19.569041, default loc),
    (51.680291, 19.572892, school),
    (51.740260, 19.452051, communicty hall),
    (51.820117, 19.292778, personal  home address),
    (51.804424, 19.211583, office block 1),
    (51.855164, 19.413300, college)
]

n = len(locations)

# Parameters
vehicle_speed_kmh = 80
max_leg_distance = 100

# Haversine distance function
def haversine(a, b):
    R = 6371
    lat1, lon1 = a
    lat2, lon2 = b

    dlat = math.radians(lat2 - lat1)
    dlon = math.radians(lon2 - lon1)

    lat1 = math.radians(lat1)
    lat2 = math.radians(lat2)

    h = (
        math.sin(dlat / 2)**2 +
        math.cos(lat1) * math.cos(lat2) *

        
# ---------------------------
# 3) Create distance matrix
# ---------------------------
n = len(locations)
distance_matrix = [[0]*n for _ in range(n)]

for i in range(n):
    for j in range(n):
        distance_matrix[i][j] = int(haversine(locations[i], locations[j])*1000)  # in meters

# ---------------------------
# 4) Create OR-Tools routing model
# ---------------------------
manager = pywrapcp.RoutingIndexManager(len(distance_matrix), 1, 0)  # 1 vehicle, depot=0
routing = pywrapcp.RoutingModel(manager)

# Distance callback
def distance_callback(from_index, to_index):
    from_node = manager.IndexToNode(from_index)
    to_node = manager.IndexToNode(to_index)
    return distance_matrix[from_node][to_node]

transit_callback_index = routing.RegisterTransitCallback(distance_callback)
routing.SetArcCostEvaluatorOfAllVehicles(transit_callback_index)

# Solve parameters
search_parameters = pywrapcp.DefaultRoutingSearchParameters()
search_parameters.first_solution_strategy = (
    routing_enums_pb2.FirstSolutionStrategy.PATH_CHEAPEST_ARC
)

# ---------------------------
# 5) Solve
# ---------------------------
solution = routing.SolveWithParameters(search_parameters)

# ---------------------------
# 6) Print best route
# ---------------------------
if solution:
    index = routing.Start(0)
    route = []
    route_distance = 0
    while not routing.IsEnd(index):
        node_index = manager.IndexToNode(index)
        route.append(node_index)
        previous_index = index
        index = solution.Value(routing.NextVar(index))
        route_distance += routing.GetArcCostForVehicle(previous_index, index, 0)
    route.append(0)  # return to depot

    print("Fastest Route (by location index):", route)
    print("Route Coordinates:")
    for i in route:
        print(locations[i])
    print("Total Distance (meters):", route_distance)
    print("Total Distance (km):", route_distance/1000)
else:
    print("No solution found!")
        math.sin(dlon / 2)**2
    )

    return 2 * R * math.asin(math.sqrt(h))
    
