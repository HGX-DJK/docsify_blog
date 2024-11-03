## 实现wgs84转gcj02系列相关

### 1、包含点、线、面的几何数据类型的转换

#### 根据数据类型判断是点、线、面的转化函数

```sql
CREATE OR REPLACE FUNCTION "public"."geoc_wgs84togcj02"("geom" "public"."geometry")
  RETURNS "public"."geometry" AS $BODY$
DECLARE
        i                 geometry;
        transform_i       geometry;
        multiArr          geometry[]; 
    
BEGIN
IF st_srid(geom) != '4490' and  st_srid(geom) != '4326'THEN
        RETURN null;
end if;
     CASE ST_GeometryType(geom)
        when 'ST_LineString' then 
            return geoc_wgs84togcj02_line(geom);
        when 'ST_MultiLineString' then 
            return geoc_wgs84togcj02_multiline(geom);
        when 'ST_Point' then 
            return geoc_wgs84togcj02_point(geom);
        when 'ST_MultiPoint' then 
            return geoc_wgs84togcj02_multipoint(geom);
        when 'ST_Polygon' then 
            return geoc_wgs84togcj02_polygon(geom);
        when 'ST_MultiPolygon' then
            return geoc_wgs84togcj02_multipolygon(geom);
        ELSE
             RETURN null;
    END CASE;
END;
$BODY$
  LANGUAGE plpgsql VOLATILE
  COST 100;
```

#### 点和多点几何对象的转换

* 点

```sql
CREATE OR REPLACE FUNCTION "public"."geoc_wgs84togcj02_point"("geom" "public"."geometry")
  RETURNS "public"."geometry" AS $BODY$
DECLARE
    lon     numeric;
    lat     numeric;
    d       jsonb;
    dlon    numeric;
    dlat    numeric;
BEGIN
    if st_geometrytype(geom) != 'ST_Point' then
        return null;
    end if;
    lon := st_x(geom);
    lat := st_y(geom);
    if (geoc_is_in_china_bbox(lon, lat) = false) then
        return geom;
    end if;
    d := geoc_delta(lon, lat);
    dlon := d->0;
    dlat := d->1;
    return st_makepoint(lon + dlon,lat + dlat);
END;
$BODY$
  LANGUAGE plpgsql VOLATILE
  COST 100;
```

* 多点

```sql
CREATE OR REPLACE FUNCTION "public"."geoc_wgs84togcj02_multipoint"("geom" "public"."geometry")
  RETURNS "public"."geometry" AS $BODY$
DECLARE
        i                 geometry;
        transform_i       geometry;
        multiArr          geometry[]; 
    
BEGIN
    multiArr:='{}'::geometry[];
    for i in EXECUTE $Q$ select (st_dump($1)).geom $Q$ using geom LOOP
          transform_i :=geoc_wgs84togcj02_point(i);
        multiArr := array_append(multiArr, transform_i);
    end LOOP;
    return st_multi(ST_Union(multiArr));
END;
$BODY$
  LANGUAGE plpgsql VOLATILE
  COST 100;
```

#### 线和多线的几何转换函数

* 线

```sql
CREATE OR REPLACE FUNCTION "public"."geoc_wgs84togcj02_line"("geom" "public"."geometry")
  RETURNS "public"."geometry" AS $BODY$
DECLARE
        p_p     geometry;
        p_t     geometry;
        z_t     geometry;
        i       int;
BEGIN
    i:=1;
    while i <= st_npoints(geom) LOOP
        p_p := st_pointn(geom,i);
        p_t := geoc_wgs84togcj02_point(p_p);
        geom:=st_setpoint(geom,i-1,p_t);
        i:=i+1;
    end LOOP;
    return geom;

END;
$BODY$
  LANGUAGE plpgsql VOLATILE
  COST 100;
```

* 多线

```sql
CREATE OR REPLACE FUNCTION "public"."geoc_wgs84togcj02_multiline"("geom" "public"."geometry")
  RETURNS "public"."geometry" AS $BODY$
DECLARE
        i                 geometry;
        transform_i       geometry;
        multiArr          geometry[]; 
    
BEGIN
    multiArr:='{}'::geometry[];
    for i in EXECUTE $Q$ select (st_dump($1)).geom $Q$ using geom LOOP
          transform_i :=geoc_wgs84togcj02_line(i);
        multiArr := array_append(multiArr, transform_i);
    end LOOP;
    return st_multi(ST_Union(multiArr));
END;
$BODY$
  LANGUAGE plpgsql VOLATILE
  COST 100;
```

#### 面和多面几何对象的转化

* 面

```sql
CREATE OR REPLACE FUNCTION "public"."geoc_wgs84togcj02_polygon"("source_geom" "public"."geometry")
  RETURNS "public"."geometry" AS $BODY$
DECLARE
    target_parts    geometry[];
    source_npoints  integer;
    single_line     geometry;
    single_line_trans geometry;
    single_polygon  geometry;
    final_geom      geometry;

BEGIN
    IF ST_GeometryType(source_geom) != 'ST_Polygon' THEN
        RETURN null;
    END IF;

    FOR single_polygon IN SELECT ST_ExteriorRing ((st_dumprings($1)).geom) as geom LOOP                
        source_npoints := ST_NPoints(single_polygon); 
        single_line  := ST_RemovePoint(single_polygon, source_npoints - 1);  
        single_line_trans := geoc_wgs84togcj02_line(single_line);  
        target_parts := array_append(target_parts, ST_AddPoint(single_line_trans, ST_PointN(single_line_trans, 1)));  
    END LOOP;
    SELECT ST_MakePolygon(target_parts[1], target_parts[2:array_upper(target_parts, 1)]) INTO final_geom; 
    RETURN final_geom;

END;
$BODY$
  LANGUAGE plpgsql VOLATILE
  COST 100;
```

* 多面
  
```sql
CREATE OR REPLACE FUNCTION "public"."geoc_wgs84togcj02_multipolygon"("source_geom" "public"."geometry")
  RETURNS "public"."geometry" AS $BODY$
DECLARE
    target_parts    geometry[];
    single_polygon  geometry;
    single_polygon_trans  geometry;
    final_geom      geometry;

BEGIN
    IF ST_GeometryType(source_geom) != 'ST_MultiPolygon' THEN
        RETURN null;
    END IF;
    FOR single_polygon IN SELECT (ST_Dump($1)).geom LOOP
        single_polygon_trans := geoc_wgs84togcj02_polygon(single_polygon); 
        target_parts := array_append(target_parts,single_polygon_trans);
    END LOOP;
    
    SELECT st_multi(ST_Union(target_parts)) INTO final_geom;
    RETURN final_geom;
END;
$BODY$
  LANGUAGE plpgsql VOLATILE
  COST 100;
```

#### 判断坐标是否在中国范围内的函数

```sql
CREATE OR REPLACE FUNCTION "public"."geoc_is_in_china_bbox"("lon" numeric, "lat" numeric)
  RETURNS "pg_catalog"."bool" AS $BODY$
DECLARE
BEGIN
    
    return lon >= 72.004 and lon <= 137.8347 and lat >= 0.8293 and lat <= 55.8271;
END;
$BODY$
  LANGUAGE plpgsql VOLATILE
  COST 100;
```

#### 坐标系之间的差异或偏移量函数

```sql
CREATE OR REPLACE FUNCTION "public"."geoc_delta"("lon" numeric, "lat" numeric)
  RETURNS "pg_catalog"."jsonb" AS $BODY$
DECLARE
    ret             varchar;
    dLon            numeric;
    dlat            numeric;
    radLat          numeric;
    magic           numeric;
    sqrtMagic       numeric;
    ee              numeric;
    a               numeric;
BEGIN
    ee := 0.006693421622965823;
    a  := 6378245;
    dLon := geoc_transform_lon(lon - 105, lat - 35);
    dLat := geoc_transform_lat(lon - 105, lat - 35);
    radLat := lat / 180 * pi();
    magic = sin(radLat);

    magic = 1 - ee * magic * magic;

    sqrtMagic := sqrt(magic);
    dLon = (dLon * 180) / (a / sqrtMagic * cos(radLat) * pi());
    dLat = (dLat * 180) / ((a * (1 - ee)) / (magic * sqrtMagic) * pi());
    ret :='['||dLon||','||dLat||']';
    return ret::jsonb;
END;
$BODY$
  LANGUAGE plpgsql VOLATILE
  COST 100;
```

#### 经度转换函数

```sql
CREATE OR REPLACE FUNCTION "public"."geoc_transform_lon"("x" numeric, "y" numeric)
  RETURNS "pg_catalog"."numeric" AS $BODY$
DECLARE
    ret   numeric;
BEGIN
    ret := 300 + x + 2 * y + 0.1 * x * x + 0.1 * x * y + 0.1 * sqrt(abs(x));
    ret :=ret + (20 * sin(6 * x * pi()) + 20 * sin(2 * x * pi())) * 2 / 3;
    ret :=ret + (20 * sin(x * pi()) + 40 * sin(x / 3 * pi())) * 2 / 3;
    ret :=ret + (150 * sin(x / 12 * pi()) + 300 * sin(x / 30 * pi())) * 2 / 3;
    return ret;
END;
$BODY$
  LANGUAGE plpgsql VOLATILE
  COST 100;
```

#### 纬度转换函数

```sql
CREATE OR REPLACE FUNCTION "public"."geoc_transform_lat"("x" numeric, "y" numeric)
  RETURNS "pg_catalog"."numeric" AS $BODY$
DECLARE
ret   numeric;
BEGIN
    ret := -100 + 2 * x + 3 * y + 0.2 * y * y + 0.1 * x * y + 0.2 * sqrt(abs(x));
    ret := ret + (20 * sin(6 * x * PI()) + 20 * sin(2 * x * PI())) * 2 / 3;
    ret := ret +(20 * sin(y * PI()) + 40 * sin(y / 3 * PI())) * 2 / 3;
    ret := ret +(160 * sin(y / 12 * PI()) + 320 * sin(y * PI() / 30)) * 2 / 3;
    return ret;

END;
$BODY$
  LANGUAGE plpgsql VOLATILE
  COST 100;
```
