import polars as pl 
import requests 
from transforms.api import transform, Output, LightweightOutput 
from transforms.external.systems import external_systems, Source, ResolvedSource 
@external_systems( 
    hormuz_rest_api=Source( 
        "<DATASET_RID>" 
    ) 
) 
@transform.using( 
    raw_vessels_hormuz=Output( 
        "<DATASET_RID>" 
    ), 
    raw_vessel_ownership=Output( 
        "<DATASET_RID>" 
    ), 
    raw_port_events=Output( 
        "<DATASET_RID>" 
    ), 
    raw_vessel_inspections=Output( 
        "<DATASET_RID>" 
    ), 
    raw_vessel_casualties=Output( 
        "<DATASET_RID>" 
    ) 
) 
def compute( 
    hormuz_rest_api: ResolvedSource, 
    raw_vessels_hormuz: LightweightOutput, 
    raw_vessel_ownership: LightweightOutput, 
    raw_port_events: LightweightOutput, 
    raw_vessel_inspections: LightweightOutput, 
    raw_vessel_casualties: LightweightOutput 
): 
    session = requests.Session() 
    session.headers.update({"Authorization": "<API_KEY>"}) 
    base_url = "https://api.vesselapi.com" 
      
    # 1. Bounding box 
      
    r = session.get( 
        f"{base_url}/v1/location/vessels/bounding-box", 
        params={ 
            "filter.latBottom": 24.9, 
            "filter.latTop":    27.2, 
            "filter.lonLeft":   55.4, 
            "filter.lonRight":  57.0, 
        } 
    ) 
    print(f"Bounding box status: {r.status_code}") 
    print(f"Response keys: {list(r.json().keys())}") 
    print(f"Bounding box error: {r.text}") 
    print(f"Vessels count: {len(r.json().get('vessels', []))}") 
    vessels = r.json().get("vessels", []) 
    df_vessels = pl.DataFrame(vessels) if vessels else pl.DataFrame() 
    raw_vessels_hormuz.write_table(df_vessels) 
      
    # 2. Port events 
      
    ports = [ 
        "IRBND",   # Bandar Abbas (Iran) 
        "AEFJR",   # Fujairah (UAE) 
        "AESHJ",   # Sharjah (UAE) 
    ] 
    all_events = [] 
    for port in ports: 
        rp = session.get(f"{base_url}/v1/portevents/port/{port}") 
        print(f"Puerto {port}: status {rp.status_code}") 
        print(f"Respuesta: {rp.text[:200]}") 
        if rp.status_code == 200: 
            port_events = rp.json().get("portEvents", []) 
            for event in port_events: 
                event["port_code"] = port 
            all_events.extend(port_events) 
    df_events = pl.DataFrame(all_events) if all_events else pl.DataFrame() 
    raw_port_events.write_table(df_events) 
      
    # 3. Ownership 
      
    imos = [v.get("imo") for v in vessels if v.get("imo")] 
    ownership_list = [] 
    for imo in imos[:20]: 
        r3 = session.get(f"{base_url}/v1/vessel/{imo}/ownership?filter.idType=imo") 
        if r3.status_code == 200: 
            ownership_list.append(r3.json().get("ownership", {})) 
    df_ownership = ( 
        pl.DataFrame(ownership_list) if ownership_list else pl.DataFrame() 
    ) 
    raw_vessel_ownership.write_table(df_ownership) 
      
    # 4. Inspections 
      
    inspections_list = [] 
    for imo in imos[:20]: 
        r4 = session.get(f"{base_url}/v1/vessel/{imo}/inspections?filter.idType=imo") 
        if r4.status_code == 200: 
            response_json = r4.json() or {} 
            inspections = response_json.get("inspections") 
            if inspections: 
                inspections_list.extend(inspections) 
    df_inspections = ( 
        pl.DataFrame(inspections_list) if inspections_list else pl.DataFrame() 
    ) 
    raw_vessel_inspections.write_table(df_inspections) 
              
    # 5. Casualties 
              
    casualties_list = [] 
    for imo in imos[:20]: 
        r5 = session.get(f"{base_url}/v1/vessel/{imo}/casualties?filter.idType=imo") 
        if r5.status_code == 200: 
            response_json = r5.json() or {} 
            casualties = response_json.get("casualties") 
            if casualties: 
                casualties_list.extend(casualties) 
    if casualties_list: 
        df_casualties = pl.DataFrame(casualties_list) 
    else: 
        df_casualties = pl.DataFrame({ 
            "imo": pl.Series([], dtype=pl.Int64), 
            "event_type": pl.Series([], dtype=pl.Utf8), 
            "date": pl.Series([], dtype=pl.Utf8), 
            "description": pl.Series([], dtype=pl.Utf8), 
            "severity": pl.Series([], dtype=pl.Utf8), 
            "pollution": pl.Series([], dtype=pl.Boolean), 
            "vessel_status": pl.Series([], dtype=pl.Utf8), 
        }) 
    raw_vessel_casualties.write_table(df_casualties) 
