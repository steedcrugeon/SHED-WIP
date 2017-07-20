using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using UnityEngine;

namespace ScienceFoundry.HyperGate
{
    public static class hGateSelector
    {
        public const string NO_TARGET = "No Hyper-gate Selected";

        public static Vessel Next(Vessel current, Vessel activeVessel)
        {
            Vessel retValue = null;

            if (activeVessel != null)
            {
                if((FlightGlobals.fetch != null) && (FlightGlobals.Vessels != null))
                {
                    var vessels = FlightGlobals.Vessels.FindAll((v) => v != null).FindAll((v) => v != activeVessel);
                    var hGates = vessels.FindAll((v) => VesselHasAnActiveGate(v));
                    if (gates.Count > 0)
                    {
                        var index = hGates.FindIndex((v) => v == current);
                        if (index >= 0)
                            retValue = hGates[(index + 1) % hGates.Count];
                        else
                            retValue = hGates[0];
                    }
                    
                }
            }

            return retValue;
        }

        private static bool VesselHasAnActiveGate(Vessel v)
        {
            bool retValue = false;

            if (v.loaded)
                retValue = CheckLoadedVesselForAhGate(v);
            else
                retValue = CheckUnloadedVesselForAhGate(v);

            return retValue;
        }

        private static bool CheckUnloadedVesselForAhGate(Vessel v)
        {
            bool retValue = false;
            const string module_name = "HyperGateModule";

            foreach (ProtoPartSnapshot pps in v.protoVessel.protoPartSnapshots)
            {
                foreach (ProtoPartModuleSnapshot m in pps.modules)
                {
                    if (m.moduleName == module_name)
                    {
                        try
                        {
                            if (!retValue)
                                retValue = Convert.ToBoolean(m.moduleValues.GetValue("hGateActivated"));
                        }
                        catch (Exception e)
                        {
                            UnityEngine.Debug.Log("Proto hGate activated: Exception: " + e.Message);
                        }
                        break;
                    }
                }
            }
            return retValue;
        }

        private static bool CheckLoadedVesselForAhGate(Vessel v)
        {
            bool retValue = false;
            const string module_name = "HyperGateModule";

            foreach (Part p in v.parts)
            {
                if (p.State != PartStates.DEAD)
                {
                    foreach (PartModule pm in p.Modules)
                    {
                        if (pm.moduleName == module_name)
                        {
                            var beacon = pm as HyperGateModule;

                            if (beacon != null)
                            {
                                if (!retValue)
                                    retValue = hGate.IshGateActive();
                            }
                            else
                                UnityEngine.Debug.Log("Not as expected a HyperGateModule");
                        }
                    }
                }
            }
            return retValue;
        }
    }
}

