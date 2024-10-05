from collections import defaultdict

office_proximity = {
    "Noida": ["Delhi", "Gurugram", "Faridabad"],
    "Delhi": ["Noida", "Gurugram", "Sonipat", "Faridabad"],
    "Sonipat": ["Delhi", "Panipat", "Gurugram"],
    "Gurugram": ["Noida", "Delhi", "Sonipat", "Panipat", "Faridabad"],
    "Panipat": ["Sonipat", "Gurugram"],
    "Faridabad": ["Delhi", "Noida", "Gurugram"]
}

holidays_per_office = {
    "Noida": [1, 3, 4, 2, 1, 5, 6, 5, 1, 7, 2, 1],
    "Delhi": [5, 1, 8, 2, 1, 7, 2, 6, 2, 8, 2, 6],
    "Sonipat": [2, 5, 8, 2, 1, 6, 9, 3, 2, 1, 5, 7],
    "Gurugram": [6, 4, 1, 6, 3, 4, 7, 3, 2, 5, 7, 8],
    "Panipat": [2, 4, 3, 1, 7, 2, 6, 8, 2, 1, 4, 6],
    "Faridabad": [2, 4, 6, 7, 2, 1, 3, 6, 3, 1, 6, 8]
}

def maximize_holidays(current_office, month, holidays_accumulated, relocations, quarter_relocations):
    if month >= 12 or relocations >= 8:
        return holidays_accumulated
    
    current_quarter = month // 3
    

    holidays_accumulated += holidays_per_office[current_office][month]
    
    next_holidays = []
    for next_office in office_proximity[current_office] + [current_office]:
        if quarter_relocations[current_quarter] < 2:
            next_holidays.append(maximize_holidays(next_office, month + 1, holidays_accumulated, relocations + (next_office != current_office), quarter_relocations))
    
    quarter_relocations[current_quarter] += 1
    
    next_holidays.append(maximize_holidays(current_office, month + 1, holidays_accumulated, relocations, quarter_relocations[:]))
    
    return max(next_holidays)

def find_best_holiday_path():
    max_holidays = 0
    best_path = []
    
    for starting_office in office_proximity.keys():
        total_holidays = maximize_holidays(starting_office, 0, 0, 0, [0] * 4)
        if total_holidays > max_holidays:
            max_holidays = total_holidays
            best_path = starting_office
    
    return max_holidays, best_path

best_holiday_count, best_starting_office = find_best_holiday_path()
print(f"Max holidays: {best_holiday_count} starting from office: {best_starting_office}")
