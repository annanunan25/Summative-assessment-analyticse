# Step 0 : Install and import necessary libraries
!pip install ortools openrouteservice ipyleaflet
from ortools.constraint_solver import routing_enums_pb2
from ortools.constraint_solver import pywrapcp
import openrouteservice
from ipyleaflet import Map, Polyline, Marker, Popup
from ipywidgets import HTML
from IPython.display import display
import time
import getpass
import folium


# Summative-assessment-analyticse
This is a repositiory used for my summative assessment analytics

from ortools.constraint_solver import pywrapcp, routing_enums_pb2
import math
python3 -m pip install ortools

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
# 3) Build distance matrix
# ---------------------------
n = len(locations)
distance_matrix = [[haversine(locations[i], locations[j]) for j in range(n)] for i in range(n)]

# ---------------------------
# 4) Create VRP model
# ---------------------------
num_vehicles = 1
depot_index = 0

manager = pywrapcp.RoutingIndexManager(len(distance_matrix), num_vehicles, depot_index)
routing = pywrapcp.RoutingModel(manager)

# Distance callback
def distance_callback(from_index, to_index):
    from_node = manager.IndexToNode(from_index)
    to_node = manager.IndexToNode(to_index)
    return distance_matrix[from_node][to_node]

transit_callback_index = routing.RegisterTransitCallback(distance_callback)
routing.SetArcCostEvaluatorOfAllVehicles(transit_callback_index)

# ---------------------------
# 5) Optional: Set per-visit distance/time limit
# ---------------------------
# For example, 1 hour per stop at 60 km/h = 60 km
max_leg_distance_meters = 60000
def distance_dimension_callback(from_index, to_index):
    return distance_matrix[manager.IndexToNode(from_index)][manager.IndexToNode(to_index)]

routing.AddDimension(
    transit_callback_index,
    0,                      # no slack
    max_leg_distance_meters, # maximum distance per vehicle
    True,                   # start cumul to zero
    "Distance"
)

# ---------------------------
# 6) Solve the VRP
# ---------------------------
search_params = pywrapcp.DefaultRoutingSearchParameters()
search_params.first_solution_strategy = routing_enums_pb2.FirstSolutionStrategy.PATH_CHEAPEST_ARC

solution = routing.SolveWithParameters(search_params)

# ---------------------------
# 7) Print the route
# ---------------------------
if solution:
    route_distance = 0
    index = routing.Start(0)
    route = []
    while not routing.IsEnd(index):
        node_index = manager.IndexToNode(index)
        route.append(node_index)
        previous_index = index
        index = solution.Value(routing.NextVar(index))
        route_distance += routing.GetArcCostForVehicle(previous_index, index, 0)
    route.append(depot_index)  # return to depot

    print("Optimal VRP Route (indices):", route)
    print("Coordinates in order:")
    for i in route:
        print(locations[i])
    print("Total Distance (meters):", route_distance)
    print("Total Distance (km):", route_distance / 1000)
else:
    print("No solution found!")
        

    
