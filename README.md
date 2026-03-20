
import React, { useMemo, useState } from "react";
import { motion } from "framer-motion";
import {
  Activity,
  AlertTriangle,
  Plane,
  Radar,
  Shield,
  Server,
  Wifi,
  Clock3,
  Gauge,
  MapPinned,
  Globe,
  Database,
  RefreshCw,
} from "lucide-react";
import {
  LineChart,
  Line,
  ResponsiveContainer,
  CartesianGrid,
  XAxis,
  YAxis,
  Tooltip,
  BarChart,
  Bar,
  AreaChart,
  Area,
} from "recharts";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Badge } from "@/components/ui/badge";
import { Input } from "@/components/ui/input";

const latencyData = [
  { time: "08:00", latency: 18, packetLoss: 0.1 },
  { time: "09:00", latency: 21, packetLoss: 0.2 },
  { time: "10:00", latency: 24, packetLoss: 0.1 },
  { time: "11:00", latency: 26, packetLoss: 0.4 },
  { time: "12:00", latency: 29, packetLoss: 0.5 },
  { time: "13:00", latency: 33, packetLoss: 0.7 },
  { time: "14:00", latency: 27, packetLoss: 0.2 },
  { time: "15:00", latency: 23, packetLoss: 0.1 },
];

const trafficData = [
  { name: "ADS-B", traffic: 86 },
  { name: "Radar", traffic: 71 },
  { name: "Voice", traffic: 58 },
  { name: "Telemetry", traffic: 77 },
  { name: "Ops VLAN", traffic: 63 },
];

const throughputData = [
  { time: "08:00", inbound: 410, outbound: 360 },
  { time: "09:00", inbound: 440, outbound: 381 },
  { time: "10:00", inbound: 470, outbound: 402 },
  { time: "11:00", inbound: 520, outbound: 445 },
  { time: "12:00", inbound: 535, outbound: 460 },
  { time: "13:00", inbound: 560, outbound: 471 },
  { time: "14:00", inbound: 498, outbound: 430 },
  { time: "15:00", inbound: 452, outbound: 389 },
];

const alerts = [
  {
    id: 1,
    severity: "High",
    title: "Latency spike detected",
    detail: "Denver sector edge router exceeded 30 ms for 12 minutes.",
    location: "DEN-EDGE-02",
    time: "15:02 MDT",
  },
  {
    id: 2,
    severity: "Medium",
    title: "Packet loss trend observed",
    detail: "Intermittent packet loss detected on telemetry aggregation link.",
    location: "PHX-AGG-01",
    time: "14:47 MDT",
  },
  {
    id: 3,
    severity: "Low",
    title: "Node maintenance status",
    detail: "Scheduled maintenance window active for backup monitoring appliance.",
    location: "TUS-MON-03",
    time: "14:10 MDT",
  },
];

const nodes = [
  {
    site: "Denver ARTCC",
    status: "Operational",
    uptime: "99.98%",
    latency: "18 ms",
    role: "Core Routing",
  },
  {
    site: "Phoenix TRACON",
    status: "Degraded",
    uptime: "99.41%",
    latency: "31 ms",
    role: "Telemetry Aggregation",
  },
  {
    site: "Tucson Ops Center",
    status: "Operational",
    uptime: "99.95%",
    latency: "16 ms",
    role: "Monitoring Node",
  },
  {
    site: "Albuquerque Sector",
    status: "Maintenance",
    uptime: "98.82%",
    latency: "22 ms",
    role: "Redundant Link",
  },
];

function statusBadge(status) {
  if (status === "Operational") return "bg-emerald-500/15 text-emerald-300 border-emerald-500/30";
  if (status === "Degraded") return "bg-amber-500/15 text-amber-300 border-amber-500/30";
  if (status === "Maintenance") return "bg-sky-500/15 text-sky-300 border-sky-500/30";
  return "bg-red-500/15 text-red-300 border-red-500/30";
}

function severityBadge(level) {
  if (level === "High") return "bg-red-500/15 text-red-300 border-red-500/30";
  if (level === "Medium") return "bg-amber-500/15 text-amber-300 border-amber-500/30";
  return "bg-slate-500/15 text-slate-300 border-slate-500/30";
}

function MetricCard({ title, value, detail, icon: Icon }) {
  return (
    <Card className="rounded-2xl border-white/10 bg-slate-900/80 shadow-xl shadow-slate-950/30 backdrop-blur">
      <CardContent className="p-5">
        <div className="flex items-start justify-between gap-4">
          <div>
            <p className="text-sm text-slate-400">{title}</p>
            <h3 className="mt-2 text-2xl font-semibold text-white">{value}</h3>
            <p className="mt-1 text-xs text-slate-400">{detail}</p>
          </div>
          <div className="rounded-2xl border border-white/10 bg-slate-800/80 p-3">
            <Icon className="h-5 w-5 text-sky-300" />
          </div>
        </div>
      </CardContent>
    </Card>
  );
}

export default function AviationNetworkDashboardMockup() {
  const [query, setQuery] = useState("");
  const [selectedView, setSelectedView] = useState("Operations");

  const filteredNodes = useMemo(() => {
    const q = query.toLowerCase().trim();
    if (!q) return nodes;
    return nodes.filter((node) =>
      [node.site, node.status, node.role].some((field) => field.toLowerCase().includes(q))
    );
  }, [query]);

  return (
    <div className="min-h-screen bg-gradient-to-br from-slate-950 via-slate-900 to-sky-950 text-slate-100">
      <div className="mx-auto max-w-7xl px-6 py-8">
        <motion.div
          initial={{ opacity: 0, y: 12 }}
          animate={{ opacity: 1, y: 0 }}
          transition={{ duration: 0.35 }}
          className="mb-8 flex flex-col gap-4 lg:flex-row lg:items-center lg:justify-between"
        >
          <div>
            <div className="mb-3 flex items-center gap-2 text-sky-300">
              <Plane className="h-5 w-5" />
              <span className="text-sm font-medium tracking-wide">Capstone Concept Demo</span>
            </div>
            <h1 className="text-3xl font-bold tracking-tight text-white md:text-4xl">
              Aviation Network Monitoring Dashboard
            </h1>
            <p className="mt-2 max-w-3xl text-sm leading-6 text-slate-300 md:text-base">
              A polished dashboard mockup showing how aviation-related network performance,
              telemetry, and alerting could be visualized in a web-based operations view.
            </p>
          </div>

          <div className="flex flex-col gap-3 sm:flex-row">
            <Input
              value={query}
              onChange={(e) => setQuery(e.target.value)}
              placeholder="Search sites, status, or role"
              className="w-full rounded-2xl border-white/10 bg-slate-900/70 text-white placeholder:text-slate-500 sm:w-72"
            />
            <div className="flex rounded-2xl border border-white/10 bg-slate-900/70 p-1">
              {["Operations", "Security", "Analytics"].map((view) => (
                <Button
                  key={view}
                  variant="ghost"
                  onClick={() => setSelectedView(view)}
                  className={`rounded-xl px-4 ${selectedView === view ? "bg-sky-400/15 text-sky-200" : "text-slate-300"}`}
                >
                  {view}
                </Button>
              ))}
            </div>
          </div>
        </motion.div>

        <div className="grid gap-4 md:grid-cols-2 xl:grid-cols-4">
          <MetricCard title="Network Status" value="Degraded" detail="1 high-priority issue requiring review" icon={Activity} />
          <MetricCard title="Average Latency" value="24 ms" detail="Past 8-hour rolling average" icon={Gauge} />
          <MetricCard title="Node Availability" value="99.54%" detail="Across monitored locations" icon={Server} />
          <MetricCard title="Security Posture" value="Stable" detail="No critical threat signatures active" icon={Shield} />
        </div>

        <div className="mt-6 grid gap-6 xl:grid-cols-[1.6fr_1fr]">
          <Card className="rounded-2xl border-white/10 bg-slate-900/80 shadow-xl shadow-slate-950/30">
            <CardHeader className="pb-2">
              <div className="flex items-center justify-between">
                <CardTitle className="text-lg text-white">Latency and Packet Loss Trends</CardTitle>
                <Badge className="border border-sky-400/20 bg-sky-400/10 text-sky-200">Live Simulated Feed</Badge>
              </div>
            </CardHeader>
            <CardContent className="h-[320px] p-4">
              <ResponsiveContainer width="100%" height="100%">
                <LineChart data={latencyData}>
                  <CartesianGrid stroke="rgba(148,163,184,0.15)" strokeDasharray="4 4" />
                  <XAxis dataKey="time" stroke="#94a3b8" fontSize={12} />
                  <YAxis stroke="#94a3b8" fontSize={12} />
                  <Tooltip
                    contentStyle={{ background: "rgba(15,23,42,0.95)", border: "1px solid rgba(148,163,184,0.2)", borderRadius: 16 }}
                  />
                  <Line type="monotone" dataKey="latency" stroke="#7dd3fc" strokeWidth={3} dot={false} />
                  <Line type="monotone" dataKey="packetLoss" stroke="#fca5a5" strokeWidth={2} dot={false} />
                </LineChart>
              </ResponsiveContainer>
            </CardContent>
          </Card>

          <Card className="rounded-2xl border-white/10 bg-slate-900/80 shadow-xl shadow-slate-950/30">
            <CardHeader className="pb-2">
              <CardTitle className="text-lg text-white">Active Alerts</CardTitle>
            </CardHeader>
            <CardContent className="space-y-3 p-4">
              {alerts.map((alert) => (
                <motion.div
                  key={alert.id}
                  initial={{ opacity: 0, x: 8 }}
                  animate={{ opacity: 1, x: 0 }}
                  className="rounded-2xl border border-white/10 bg-slate-950/50 p-4"
                >
                  <div className="mb-2 flex items-center justify-between gap-3">
                    <div className="flex items-center gap-2">
                      <AlertTriangle className="h-4 w-4 text-amber-300" />
                      <p className="font-medium text-white">{alert.title}</p>
                    </div>
                    <Badge className={`border ${severityBadge(alert.severity)}`}>{alert.severity}</Badge>
                  </div>
                  <p className="text-sm text-slate-300">{alert.detail}</p>
                  <div className="mt-3 flex items-center justify-between text-xs text-slate-400">
                    <span>{alert.location}</span>
                    <span>{alert.time}</span>
                  </div>
                </motion.div>
              ))}
            </CardContent>
          </Card>
        </div>

        <div className="mt-6 grid gap-6 xl:grid-cols-[1.2fr_1fr]">
          <Card className="rounded-2xl border-white/10 bg-slate-900/80 shadow-xl shadow-slate-950/30">
            <CardHeader className="pb-2">
              <div className="flex items-center justify-between">
                <CardTitle className="text-lg text-white">Monitored Nodes</CardTitle>
                <Badge className="border border-white/10 bg-slate-800 text-slate-200">{filteredNodes.length} displayed</Badge>
              </div>
            </CardHeader>
            <CardContent className="p-4">
              <div className="overflow-hidden rounded-2xl border border-white/10">
                <table className="w-full text-left text-sm">
                  <thead className="bg-slate-950/70 text-slate-400">
                    <tr>
                      <th className="px-4 py-3 font-medium">Site</th>
                      <th className="px-4 py-3 font-medium">Status</th>
                      <th className="px-4 py-3 font-medium">Uptime</th>
                      <th className="px-4 py-3 font-medium">Latency</th>
                      <th className="px-4 py-3 font-medium">Role</th>
                    </tr>
                  </thead>
                  <tbody>
                    {filteredNodes.map((node) => (
                      <tr key={node.site} className="border-t border-white/10 bg-slate-900/30">
                        <td className="px-4 py-4 text-white">{node.site}</td>
                        <td className="px-4 py-4">
                          <Badge className={`border ${statusBadge(node.status)}`}>{node.status}</Badge>
                        </td>
                        <td className="px-4 py-4 text-slate-300">{node.uptime}</td>
                        <td className="px-4 py-4 text-slate-300">{node.latency}</td>
                        <td className="px-4 py-4 text-slate-300">{node.role}</td>
                      </tr>
                    ))}
                  </tbody>
                </table>
              </div>
            </CardContent>
          </Card>

          <div className="grid gap-6">
            <Card className="rounded-2xl border-white/10 bg-slate-900/80 shadow-xl shadow-slate-950/30">
              <CardHeader className="pb-2">
                <CardTitle className="text-lg text-white">Traffic by Service</CardTitle>
              </CardHeader>
              <CardContent className="h-[220px] p-4">
                <ResponsiveContainer width="100%" height="100%">
                  <BarChart data={trafficData}>
                    <CartesianGrid stroke="rgba(148,163,184,0.15)" strokeDasharray="4 4" />
                    <XAxis dataKey="name" stroke="#94a3b8" fontSize={12} />
                    <YAxis stroke="#94a3b8" fontSize={12} />
                    <Tooltip
                      contentStyle={{ background: "rgba(15,23,42,0.95)", border: "1px solid rgba(148,163,184,0.2)", borderRadius: 16 }}
                    />
                    <Bar dataKey="traffic" fill="#38bdf8" radius={[8, 8, 0, 0]} />
                  </BarChart>
                </ResponsiveContainer>
              </CardContent>
            </Card>

            <Card className="rounded-2xl border-white/10 bg-slate-900/80 shadow-xl shadow-slate-950/30">
              <CardHeader className="pb-2">
                <CardTitle className="text-lg text-white">Operational Summary</CardTitle>
              </CardHeader>
              <CardContent className="space-y-4 p-4 text-sm text-slate-300">
                <div className="flex items-start gap-3">
                  <Radar className="mt-0.5 h-4 w-4 text-sky-300" />
                  <p>Telemetry monitoring highlights one degraded site with elevated latency and intermittent packet loss.</p>
                </div>
                <div className="flex items-start gap-3">
                  <Wifi className="mt-0.5 h-4 w-4 text-sky-300" />
                  <p>Core services remain stable with acceptable throughput across most monitored segments.</p>
                </div>
                <div className="flex items-start gap-3">
                  <MapPinned className="mt-0.5 h-4 w-4 text-sky-300" />
                  <p>All sites shown are sample nodes for capstone demonstration only and do not represent live FAA systems.</p>
                </div>
                <div className="flex items-start gap-3">
                  <Clock3 className="mt-0.5 h-4 w-4 text-sky-300" />
                  <p>The dashboard can later be extended to simulated streaming data, static JSON, or API-driven telemetry feeds.</p>
                </div>
              </CardContent>
            </Card>
          </div>
        </div>

        <div className="mt-6 grid gap-6 lg:grid-cols-2">
          <Card className="rounded-2xl border-white/10 bg-slate-900/80 shadow-xl shadow-slate-950/30">
            <CardHeader className="pb-2">
              <CardTitle className="text-lg text-white">Throughput Overview</CardTitle>
            </CardHeader>
            <CardContent className="h-[260px] p-4">
              <ResponsiveContainer width="100%" height="100%">
                <AreaChart data={throughputData}>
                  <CartesianGrid stroke="rgba(148,163,184,0.15)" strokeDasharray="4 4" />
                  <XAxis dataKey="time" stroke="#94a3b8" fontSize={12} />
                  <YAxis stroke="#94a3b8" fontSize={12} />
                  <Tooltip
                    contentStyle={{ background: "rgba(15,23,42,0.95)", border: "1px solid rgba(148,163,184,0.2)", borderRadius: 16 }}
                  />
                  <Area type="monotone" dataKey="inbound" stroke="#22d3ee" fill="#22d3ee33" strokeWidth={2} />
                  <Area type="monotone" dataKey="outbound" stroke="#60a5fa" fill="#60a5fa22" strokeWidth={2} />
                </AreaChart>
              </ResponsiveContainer>
            </CardContent>
          </Card>

          <Card className="rounded-2xl border-white/10 bg-slate-900/80 shadow-xl shadow-slate-950/30">
            <CardHeader className="pb-2">
              <CardTitle className="text-lg text-white">Project Scope Snapshot</CardTitle>
            </CardHeader>
            <CardContent className="grid gap-4 p-4 md:grid-cols-2">
              <div className="rounded-2xl border border-white/10 bg-slate-950/50 p-4">
                <div className="mb-2 flex items-center gap-2 text-sky-300">
                  <Globe className="h-4 w-4" />
                  <span className="text-sm font-medium">Frontend Focus</span>
                </div>
                <p className="text-sm text-slate-300">
                  Demonstrates how a modern monitoring dashboard could present system health, site status, and active alerts.
                </p>
              </div>
              <div className="rounded-2xl border border-white/10 bg-slate-950/50 p-4">
                <div className="mb-2 flex items-center gap-2 text-sky-300">
                  <Database className="h-4 w-4" />
                  <span className="text-sm font-medium">Data Model</span>
                </div>
                <p className="text-sm text-slate-300">
                  Uses mock datasets now, but can be upgraded later to JSON feeds, CSV imports, or lightweight APIs.
                </p>
              </div>
              <div className="rounded-2xl border border-white/10 bg-slate-950/50 p-4">
                <div className="mb-2 flex items-center gap-2 text-sky-300">
                  <RefreshCw className="h-4 w-4" />
                  <span className="text-sm font-medium">Demo Ready</span>
                </div>
                <p className="text-sm text-slate-300">
                  Good for GitHub Pages, capstone screenshots, class presentations, and showing the intended visual outcome.
                </p>
              </div>
              <div className="rounded-2xl border border-white/10 bg-slate-950/50 p-4">
                <div className="mb-2 flex items-center gap-2 text-sky-300">
                  <Shield className="h-4 w-4" />
                  <span className="text-sm font-medium">Safe Scope</span>
                </div>
                <p className="text-sm text-slate-300">
                  Avoids real operational data and keeps the project presentation-focused while still looking realistic and technical.
                </p>
              </div>
            </CardContent>
          </Card>
        </div>
      </div>
    </div>
  );
}
