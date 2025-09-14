[frontend end.txt](https://github.com/user-attachments/files/22321730/frontend.end.txt)
import React, { useState } from "react";

const years = [
  "1st Year",
  "2nd Year",
  "3rd Year",
  "4th Year",
  "Diploma",
  "MCA",
  "MBA",
];
const branches = [
  "CSE",
  "ECE",
  "ME",
  "CE",
  "EE",
  "Diploma",
  "MCA",
  "MBA",
];

const daysOfWeek = [
  "Monday",
  "Tuesday",
  "Wednesday",
  "Thursday",
  "Friday",
  "Saturday",
];

const btechMorning = [
  "09:00 - 10:00",
  "10:00 - 11:00",
  "11:00 - 12:00",
];
const btechLunch = "12:00 - 12:50";
const btechAfternoon = [
  "12:50 - 13:40",
  "13:40 - 14:30",
  "14:30 - 15:20",
];
const lastSlot = "15:30 - 16:00";

const mcaMbaPeriods = [
  "09:00 - 10:00",
  "10:00 - 11:00",
  "11:00 - 12:00",
  "12:50 - 13:50",
  "13:50 - 14:50",
];
const mcaMbaLunch = "12:00 - 12:50";

const specialActs = [
  "Games",
  "Library",
  "Simple Coding Sessions",
  "Communication Activities (JAM, GD, SD)",
];

function App() {
  const [selectedYear, setSelectedYear] = useState("");
  const [selectedBranch, setSelectedBranch] = useState("");
  const [sections, setSections] = useState(["Section 1"]);
  const [facultyList, setFacultyList] = useState([
    { name: "", subject: "" },
  ]);
  const [facultyAssign, setFacultyAssign] = useState({});
  const [step, setStep] = useState(1);

  // Step 1 handlers
  const handleAddSection = () =>
    setSections([...sections, `Section ${sections.length + 1}`]);
  const handleFacultyChange = (idx, key, value) => {
    setFacultyList((prev) =>
      prev.map((row, i) => (i === idx ? { ...row, [key]: value } : row))
    );
  };
  const handleAddFaculty = () =>
    setFacultyList([...facultyList, { name: "", subject: "" }]);
  const handleSubmitStep1 = (e) => {
    e.preventDefault();
    const assignInit = {};
    sections.forEach((sec) => {
      assignInit[sec] = {};
      daysOfWeek.forEach((day) => {
        assignInit[sec][day] = {};
        let periods = [];
        if (selectedYear === "4th Year") {
          periods = [
            ...btechMorning,
            btechLunch,
            "12:50 - 16:00",
            lastSlot,
          ];
        } else if (selectedYear === "MBA" || selectedYear === "MCA") {
          periods = [
            ...mcaMbaPeriods.slice(0, 3),
            mcaMbaLunch,
            ...mcaMbaPeriods.slice(3),
            lastSlot,
          ];
        } else {
          periods = [
            ...btechMorning,
            btechLunch,
            ...btechAfternoon,
            lastSlot,
          ];
        }
        periods.forEach((period) => {
          assignInit[sec][day][period] = "";
        });
      });
    });
    setFacultyAssign(assignInit);
    setStep(2);
  };

  // Step 2 handlers
  const handleAssignChange = (sec, day, period, value) => {
    setFacultyAssign((prev) => ({
      ...prev,
      [sec]: {
        ...prev[sec],
        [day]: {
          ...prev[sec][day],
          [period]: value,
        },
      },
    }));
  };
  const countFacultyPerDay = (sec, day, name) =>
    Object.values(facultyAssign[sec][day]).filter((v) => v === name).length;

  const handleValidateAndShowTimetable = (e) => {
    e.preventDefault();
    for (const sec of sections) {
      for (const day of daysOfWeek) {
        for (const f of facultyList) {
          const name = f.name.trim();
          const cnt = countFacultyPerDay(sec, day, name);
          if (name && cnt > 2)
            return alert(
              `Faculty "${name}" assigned ${cnt} classes on ${day} in ${sec} (max 2 allowed).`
            );
        }
      }
    }
    setStep(3);
  };

  if (step === 1)
    return (
      <div style={{ maxWidth: 700, margin: "auto" }}>
        <h2>Timetable Generator - Step 1</h2>
        <form onSubmit={handleSubmitStep1}>
          <label>
            Year:{" "}
            <select
              required
              value={selectedYear}
              onChange={(e) => setSelectedYear(e.target.value)}
            >
              <option value="">--Year--</option>
              {years.map((y) => (
                <option key={y}>{y}</option>
              ))}
            </select>
          </label>{" "}
          <label>
            Branch:{" "}
            <select
              required
              value={selectedBranch}
              onChange={(e) => setSelectedBranch(e.target.value)}
            >
              <option value="">--Branch--</option>
              {branches.map((b) => (
                <option key={b}>{b}</option>
              ))}
            </select>
          </label>
          <br />
          <h4>Sections:</h4>
          {sections.map((sec) => (
            <span key={sec}>{sec} </span>
          ))}
          <button type="button" onClick={handleAddSection}>
            Add Section
          </button>
          <h4>Faculty & Subject:</h4>
          {facultyList.map((row, idx) => (
            <div key={idx}>
              <input
                type="text"
                required
                placeholder="Faculty Name"
                value={row.name}
                onChange={(e) =>
                  handleFacultyChange(idx, "name", e.target.value)
                }
              />{" "}
              <input
                type="text"
                required
                placeholder="Subject"
                value={row.subject}
                onChange={(e) =>
                  handleFacultyChange(idx, "subject", e.target.value)
                }
              />
            </div>
          ))}
          <button type="button" onClick={handleAddFaculty}>
            Add Faculty
          </button>
          <br />
          <button type="submit" style={{ marginTop: 20 }}>
            Next: Assign Faculty to Periods
          </button>
        </form>
      </div>
    );

  if (step === 2)
    return (
      <div style={{ maxWidth: 1200, margin: "auto" }}>
        <h2>Step 2: Assign Faculty to Each Period</h2>
        <form onSubmit={handleValidateAndShowTimetable}>
          {sections.map((sec) => (
            <div key={sec} style={{ marginBottom: 35 }}>
              <h3 style={{ background: "#f0f5ff" }}>{sec}</h3>
              <table border="1" cellPadding="4" style={{ width: "100%" }}>
                <thead>
                  <tr>
                    <th>Day / Time</th>
                    {Object.keys(facultyAssign[sec][daysOfWeek[0]]).map(
                      (period) => (
                        <th key={period}>{period}</th>
                      )
                    )}
                  </tr>
                </thead>
                <tbody>
                  {daysOfWeek.map((day) => (
                    <tr key={day}>
                      <td>
                        <b>{day}</b>
                      </td>
                      {Object.keys(facultyAssign[sec][day]).map((period) => {
                        const isLunch =
                          period === btechLunch || period === mcaMbaLunch;
                        const isCRT =
                          selectedYear === "4th Year" &&
                          period === "12:50 - 16:00";
                        const isLast = period === lastSlot;
                        if (isLast) {
                          const idx =
                            (daysOfWeek.indexOf(day) + sections.indexOf(sec)) %
                            specialActs.length;
                          return (
                            <td key={period}>
                              <i>{specialActs[idx]}</i>
                            </td>
                          );
                        }
                        if (isLunch)
                          return (
                            <td key={period} style={{ background: "#ffe9cf" }}>
                              <i>Lunch Break</i>
                            </td>
                          );
                        if (isCRT)
                          return (
                            <td key={period} style={{ background: "#e3fcec" }}>
                              <i>CRT/Placement Training</i>
                            </td>
                          );
                        return (
                          <td key={period}>
                            <select
                              value={facultyAssign[sec][day][period]}
                              onChange={(e) =>
                                handleAssignChange(
                                  sec,
                                  day,
                                  period,
                                  e.target.value
                                )
                              }
                              style={{
                                background:
                                  facultyAssign[sec][day][period] &&
                                  countFacultyPerDay(
                                    sec,
                                    day,
                                    facultyAssign[sec][day][period]
                                  ) > 2
                                    ? "#ffe4e4"
                                    : undefined,
                              }}
                            >
                              <option value="">-None-</option>
                              {facultyList.map((f) => (
                                <option key={f.name + f.subject} value={f.name}>
                                  {f.name} ({f.subject})
                                </option>
                              ))}
                            </select>
                          </td>
                        );
                      })}
                    </tr>
                  ))}
                </tbody>
              </table>
            </div>
          ))}
          <button type="submit" style={{ padding: "8px 18px", fontSize: 15 }}>
            Generate Timetable
          </button>
        </form>
      </div>
    );

  return (
    <div style={{ maxWidth: 1200, margin: "auto" }}>
      <h2>Generated Timetable</h2>
      {sections.map((sec) => (
        <div key={sec} style={{ marginBottom: 35 }}>
          <h3 style={{ background: "#eef7fa" }}>{`Year: ${selectedYear}, Branch: ${selectedBranch}, ${sec}`}</h3>
          <table border="1" cellPadding="4" style={{ width: "100%" }}>
            <thead>
              <tr>
                <th>Day / Time</th>
                {Object.keys(facultyAssign[sec][daysOfWeek[0]]).map(
                  (period) => (
                    <th key={period}>{period}</th>
                  )
                )}
              </tr>
            </thead>
            <tbody>
              {daysOfWeek.map((day) => (
                <tr key={day}>
                  <td>
                    <b>{day}</b>
                  </td>
                  {Object.keys(facultyAssign[sec][day]).map((period) => {
                    const val = facultyAssign[sec][day][period];
                    const isLunch =
                      period === btechLunch || period === mcaMbaLunch;
                    const isCRT =
                      selectedYear === "4th Year" &&
                      period === "12:50 - 16:00";
                    const isLast = period === lastSlot;
                    const facultyObj = facultyList.find(
                      (f) => f.name === val
                    );
                    const subj = facultyObj ? facultyObj.subject : "";
                    if (isLunch)
                      return (
                        <td key={period} style={{ background: "#ffe9cf" }}>
                          <i>Lunch Break</i>
                        </td>
                      );
                    if (isCRT)
                      return (
                        <td key={period} style={{ background: "#e3fcec" }}>
                          <i>CRT/Placement Training</i>
                        </td>
                      );
                    if (isLast) {
                      const idx =
                        (daysOfWeek.indexOf(day) + sections.indexOf(sec)) %
                        specialActs.length;
                      return (
                        <td key={period}>
                          <b>{specialActs[idx]}</b>
                        </td>
                      );
                    }
                    return (
                      <td key={period}>
                        {val && subj ? (
                          <>
                            <b>{subj}</b> <br />
                            <span style={{ color: "#005" }}>{val}</span>
                          </>
                        ) : (
                          "-"
                        )}
                      </td>
                    );
                  })}
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      ))}

      <button onClick={() => setStep(1)} style={{ marginTop: 15 }}>
        Start Over
      </button>
    </div>
  );
}

export default App;
