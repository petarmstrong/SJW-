# SJW-
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>St Johns Wharf - Tenant Guide</title>
    <!-- React & ReactDOM -->
    <script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    <!-- Babel for JSX -->
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap');
        body { font-family: 'Inter', sans-serif; background-color: #f3f4f6; }
        .tap-highlight-transparent { -webkit-tap-highlight-color: transparent; }
        /* Smooth fade in animation */
        @keyframes fadeIn { from { opacity: 0; transform: translateY(-10px); } to { opacity: 1; transform: translateY(0); } }
        .animate-fadeIn { animation: fadeIn 0.3s ease-out forwards; }
    </style>
</head>
<body>
    <div id="root"></div>

    <script type="text/babel">
        const { useState, useEffect } = React;

        // --- Icons (Embedded for reliability) ---
        const Icon = ({ children, className }) => (
            <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className={className}>
                {children}
            </svg>
        );

        const Home = (props) => <Icon {...props}><path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"></path><polyline points="9 22 9 12 15 12 15 22"></polyline></Icon>;
        const MapPin = (props) => <Icon {...props}><path d="M21 10c0 7-9 13-9 13s-9-6-9-13a9 9 0 0 1 18 0z"></path><circle cx="12" cy="10" r="3"></circle></Icon>;
        const Copy = (props) => <Icon {...props}><rect x="9" y="9" width="13" height="13" rx="2" ry="2"></rect><path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"></path></Icon>;
        const Trash2 = (props) => <Icon {...props}><polyline points="3 6 5 6 21 6"></polyline><path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path><line x1="10" y1="11" x2="10" y2="17"></line><line x1="14" y1="11" x2="14" y2="17"></line></Icon>;
        const Zap = (props) => <Icon {...props}><polygon points="13 2 3 14 12 14 11 22 21 10 12 10 13 2"></polygon></Icon>;
        const Phone = (props) => <Icon {...props}><path d="M22 16.92v3a2 2 0 0 1-2.18 2 19.79 19.79 0 0 1-8.63-3.07 19.5 19.5 0 0 1-6-6 19.79 19.79 0 0 1-3.07-8.67A2 2 0 0 1 4.11 2h3a2 2 0 0 1 2 1.72 12.84 12.84 0 0 0 .7 2.81 2 2 0 0 1-.45 2.11L8.09 9.91a16 16 0 0 0 6 6l1.27-1.27a2 2 0 0 1 2.11-.45 12.84 12.84 0 0 0 2.81.7A2 2 0 0 1 22 16.92z"></path></Icon>;
        const Mail = (props) => <Icon {...props}><path d="M4 4h16c1.1 0 2 .9 2 2v12c0 1.1-.9 2-2 2H4c-1.1 0-2-.9-2-2V6c0-1.1.9-2 2-2z"></path><polyline points="22,6 12,13 2,6"></polyline></Icon>;
        const Shield = (props) => <Icon {...props}><path d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10z"></path></Icon>;
        const AlertTriangle = (props) => <Icon {...props}><path d="M10.29 3.86L1.82 18a2 2 0 0 0 1.71 3h16.94a2 2 0 0 0 1.71-3L13.71 3.86a2 2 0 0 0-3.42 0z"></path><line x1="12" y1="9" x2="12" y2="13"></line><line x1="12" y1="17" x2="12.01" y2="17"></line></Icon>;
        const ChevronDown = (props) => <Icon {...props}><polyline points="6 9 12 15 18 9"></polyline></Icon>;
        const ChevronUp = (props) => <Icon {...props}><polyline points="18 15 12 9 6 15"></polyline></Icon>;
        const Check = (props) => <Icon {...props}><polyline points="20 6 9 17 4 12"></polyline></Icon>;


        // --- Components ---

        const SectionHeader = ({ icon: Icon, title, isOpen, toggle }) => (
            <button 
                onClick={toggle}
                className="w-full flex items-center justify-between p-4 bg-white border-b border-gray-100 hover:bg-gray-50 transition-colors tap-highlight-transparent"
            >
                <div className="flex items-center gap-3">
                    <div className="p-2 bg-blue-50 text-blue-600 rounded-lg">
                        <Icon />
                    </div>
                    <span className="font-semibold text-gray-800">{title}</span>
                </div>
                {isOpen ? <ChevronUp className="text-gray-400"/> : <ChevronDown className="text-gray-400"/>}
            </button>
        );

        const CodeCard = ({ label, code }) => {
            const [copied, setCopied] = useState(false);

            const handleCopy = () => {
                const textArea = document.createElement("textarea");
                textArea.value = code;
                document.body.appendChild(textArea);
                textArea.select();
                try {
                    document.execCommand('copy');
                    setCopied(true);
                    setTimeout(() => setCopied(false), 2000);
                } catch (err) {
                    console.error('Unable to copy', err);
                }
                document.body.removeChild(textArea);
            };

            return (
                <div 
                    onClick={handleCopy}
                    className="bg-white p-4 rounded-xl border border-gray-200 shadow-sm active:scale-95 transition-transform cursor-pointer flex justify-between items-center group"
                >
                    <div>
                        <p className="text-xs text-gray-500 uppercase tracking-wider font-semibold mb-1">{label}</p>
                        <p className="text-2xl font-bold text-gray-800 tracking-tight">{code}</p>
                    </div>
                    <div className={`p-2 rounded-full transition-colors ${copied ? 'bg-green-100 text-green-600' : 'bg-gray-100 text-gray-400 group-hover:bg-blue-50 group-hover:text-blue-600'}`}>
                        {copied ? <Check /> : <Copy />}
                    </div>
                </div>
            );
        };

        const ContactRow = ({ name, number, subtext }) => (
            <a href={`tel:${number.replace(/\s/g, '')}`} className="flex items-center justify-between p-3 bg-gray-50 rounded-lg mb-2 hover:bg-blue-50 transition-colors border border-gray-100">
                <div>
                    <p className="font-medium text-gray-800">{name}</p>
                    {subtext && <p className="text-xs text-gray-500">{subtext}</p>}
                </div>
                <div className="flex items-center gap-2 text-blue-600 font-semibold bg-white px-3 py-1 rounded-full shadow-sm">
                    <Phone />
                    <span className="text-sm">{number}</span>
                </div>
            </a>
        );

        const InfoBlock = ({ title, children, warning }) => (
            <div className={`mb-4 p-4 rounded-lg border ${warning ? 'bg-amber-50 border-amber-200' : 'bg-gray-50 border-gray-100'}`}>
                <h4 className={`font-semibold mb-2 ${warning ? 'text-amber-800' : 'text-gray-700'}`}>{title}</h4>
                <div className="text-sm text-gray-600 space-y-2 leading-relaxed">
                    {children}
                </div>
            </div>
        );

        const App = () => {
            const [openSection, setOpenSection] = useState('codes'); 

            const toggle = (section) => {
                setOpenSection(openSection === section ? null : section);
            };

            return (
                <div className="max-w-md mx-auto bg-gray-100 min-h-screen pb-12 shadow-2xl overflow-hidden relative">
                    
                    {/* Header Image/Banner Area */}
                    <div className="bg-slate-800 text-white p-6 pt-10 pb-16 relative overflow-hidden">
                        <div className="absolute top-0 right-0 p-8 opacity-10">
                            <Home className="w-32 h-32" />
                        </div>
                        <h1 className="text-3xl font-bold mb-2 relative z-10">St Johns Wharf</h1>
                        <p className="text-blue-200 mb-4 relative z-10">Tenant Information Guide</p>
                        
                        <a 
                            href="https://www.google.com/maps/search/?api=1&query=1-3+Laganbank+Road+Belfast+BT1+3LT" 
                            target="_blank"
                            className="inline-flex items-center gap-2 bg-slate-700/50 hover:bg-slate-700 backdrop-blur-sm px-3 py-1.5 rounded-full text-xs text-blue-100 border border-slate-600 transition-colors"
                        >
                            <MapPin className="w-3 h-3" />
                            1–3 Laganbank Road, Belfast, BT1 3LT
                        </a>
                    </div>

                    {/* Main Content Container */}
                    <div className="relative -mt-8 px-4 space-y-4 z-20">

                        {/* Welcome Card */}
                        <div className="bg-white rounded-xl shadow-sm border border-gray-200 p-5">
                            <h2 className="font-semibold text-gray-800 mb-2">Welcome Home</h2>
                            <p className="text-sm text-gray-600 leading-relaxed">
                                We are delighted to have you here! This interactive guide covers everything from door codes to recycling to help you settle into our vibrant riverside community.
                            </p>
                        </div>

                        {/* Access Codes Section */}
                        <div className="space-y-3">
                            <h3 className="text-sm font-bold text-gray-500 uppercase tracking-wider ml-1">Quick Access Codes</h3>
                            <div className="grid grid-cols-2 gap-3">
                                <CodeCard label="Main Building" code="1024" />
                                <CodeCard label="Pedestrian Gate" code="1256" />
                                <CodeCard label="Vehicle Gate" code="4561" />
                                <CodeCard label="Garage Door" code="C14589" />
                                <CodeCard label="Bin Store" code="C14589" />
                            </div>
                        </div>

                        {/* Accordion Sections */}
                        <div className="bg-white rounded-xl shadow-sm border border-gray-200 overflow-hidden">
                            
                            {/* Bins & Recycling */}
                            <SectionHeader 
                                icon={Trash2} 
                                title="Bins & Recycling" 
                                isOpen={openSection === 'bins'} 
                                toggle={() => toggle('bins')} 
                            />
                            {openSection === 'bins' && (
                                <div className="p-5 animate-fadeIn">
                                    <InfoBlock title="Collection Schedule">
                                        <p><strong>Collection Day:</strong> Monday</p>
                                        <p>Bins are managed by a waste company. You do not need to move them.</p>
                                    </InfoBlock>

                                    <InfoBlock title="Rules" warning={true}>
                                        <ul className="list-disc pl-4 space-y-1">
                                            <li>Keep lids <strong>fully closed</strong> or they won't be collected.</li>
                                            <li>Do not leave waste beside bins.</li>
                                            <li><strong>£250 Fine</strong> for dumping furniture or non-domestic waste.</li>
                                        </ul>
                                    </InfoBlock>

                                    <InfoBlock title="Recycling Guide">
                                        <div className="grid grid-cols-2 gap-2 text-xs">
                                            <div className="bg-green-50 p-2 rounded text-green-800 border border-green-100">
                                                <strong>YES - Recycle:</strong>
                                                <ul className="mt-1 space-y-1">
                                                    <li>• Paper & Cardboard</li>
                                                    <li>• Tins & Cans</li>
                                                    <li>• Plastic Bottles/Tubs</li>
                                                    <li>• Tetra Pak cartons</li>
                                                </ul>
                                            </div>
                                            <div className="bg-red-50 p-2 rounded text-red-800 border border-red-100">
                                                <strong>NO - General Waste:</strong>
                                                <ul className="mt-1 space-y-1">
                                                    <li>• Plastic bags</li>
                                                    <li>• Polystyrene</li>
                                                    <li>• Food-soiled items</li>
                                                    <li>• Glass (Use box)</li>
                                                </ul>
                                            </div>
                                        </div>
                                    </InfoBlock>
                                    
                                    <div className="mt-4 text-center">
                                        <a href="https://www.belfastcity.gov.uk/recycling" target="_blank" className="text-blue-600 text-sm hover:underline font-medium">
                                            More info at Belfast City Council &rarr;
                                        </a>
                                    </div>
                                </div>
                            )}

                            {/* Utilities */}
                            <SectionHeader 
                                icon={Zap} 
                                title="Utilities & Safety" 
                                isOpen={openSection === 'utilities'} 
                                toggle={() => toggle('utilities')} 
                            />
                            {openSection === 'utilities' && (
                                <div className="p-5 animate-fadeIn">
                                    <InfoBlock title="Meters">
                                        <p><strong>Electric:</strong> Electric rooms in basement garages (check apt number).</p>
                                        <p><strong>Gas:</strong> Communal hallways outside your door.</p>
                                    </InfoBlock>
                                    
                                    <InfoBlock title="Emergency Shut-offs" warning={true}>
                                        <div className="flex items-start gap-2">
                                            <AlertTriangle className="mt-1 shrink-0 text-amber-600" />
                                            <p><strong>Water Stopcock:</strong> Locate this immediately inside your apartment. Turn off if there is a leak.</p>
                                        </div>
                                        <div className="flex items-start gap-2 mt-2">
                                            <Zap className="mt-1 shrink-0 text-amber-600" />
                                            <p><strong>Trip Switch:</strong> Located inside your apartment.</p>
                                        </div>
                                    </InfoBlock>
                                </div>
                            )}

                            {/* Intercom & Parking */}
                            <SectionHeader 
                                icon={Phone} 
                                title="Intercom & Parking" 
                                isOpen={openSection === 'intercom'} 
                                toggle={() => toggle('intercom')} 
                            />
                            {openSection === 'intercom' && (
                                <div className="p-5 animate-fadeIn">
                                    <InfoBlock title="Intercom System">
                                        <p>Linked to your phone. When a visitor calls, answer and follow instructions to grant access.</p>
                                        <div className="mt-3 p-3 bg-blue-50 rounded border border-blue-100">
                                            <p className="text-xs text-blue-800 mb-1 font-bold">Need to update your number?</p>
                                            <a href="mailto:victoria.stevenson@mcguinnessfleck.com" className="flex items-center gap-2 text-blue-600 font-medium text-sm">
                                                <Mail className="w-4 h-4" /> Email Victoria Stevenson
                                            </a>
                                        </div>
                                    </InfoBlock>
                                    <InfoBlock title="Parking">
                                        <p>Park <strong>only</strong> in your allocated numbered space.</p>
                                        <p>Visitor spaces are first-come, first-served (no long-term parking).</p>
                                    </InfoBlock>
                                </div>
                            )}

                             {/* Security & Living */}
                            <SectionHeader 
                                icon={Shield} 
                                title="Security & Living" 
                                isOpen={openSection === 'security'} 
                                toggle={() => toggle('security')} 
                            />
                            {openSection === 'security' && (
                                <div className="p-5 animate-fadeIn">
                                    <ul className="space-y-3 text-sm text-gray-700">
                                        <li className="flex gap-2">
                                            <span className="text-blue-500">•</span>
                                            <span><strong>Doors:</strong> Ensure all gates and cupboards close securely. Do not wedge doors open.</span>
                                        </li>
                                        <li className="flex gap-2">
                                            <span className="text-blue-500">•</span>
                                            <span><strong>Noise:</strong> Please respect neighbours, especially at night.</span>
                                        </li>
                                        <li className="flex gap-2">
                                            <span className="text-blue-500">•</span>
                                            <span><strong>Pests:</strong> Do not leave food waste in hallways. Keep balcony doors closed when food is out. Report pests immediately.</span>
                                        </li>
                                        <li className="flex gap-2">
                                            <span className="text-blue-500">•</span>
                                            <span><strong>Mail:</strong> Collect mail regularly from boxes outside your door.</span>
                                        </li>
                                    </ul>
                                </div>
                            )}

                            {/* Contact Numbers */}
                            <SectionHeader 
                                icon={Phone} 
                                title="Useful Contacts" 
                                isOpen={openSection === 'contacts'} 
                                toggle={() => toggle('contacts')} 
                            />
                            {openSection === 'contacts' && (
                                <div className="p-5 animate-fadeIn">
                                    <h4 className="text-xs font-bold text-gray-400 uppercase tracking-wider mb-3">Management</h4>
                                    <ContactRow name="McGuinness Fleck" number="028 9068 3020" subtext="Property Management" />
                                    <ContactRow name="Out of Hours" number="07889 551 245" subtext="Emergency Only" />
                                    
                                    <h4 className="text-xs font-bold text-gray-400 uppercase tracking-wider mb-3 mt-6">Utilities</h4>
                                    <ContactRow name="Phoenix Energy" number="03454 555 555" subtext="Gas" />
                                    <ContactRow name="NI Electricity" number="03457 643 643" subtext="Faults & Emergencies" />
                                    <ContactRow name="Power NI" number="03457 455 455" />
                                    <ContactRow name="SSE Airtricity" number="0345 601 9093" />
                                    <ContactRow name="Budget Energy" number="0800 012 1177" />
                                    <ContactRow name="Belfast City Council" number="028 9032 0202" subtext="Bins" />
                                </div>
                            )}

                        </div>
                        
                        {/* Footer / Owners Info */}
                        <div className="bg-slate-200 rounded-xl p-5 text-center mt-6 mb-10">
                            <h3 className="font-semibold text-slate-700 mb-2">For Property Owners</h3>
                            <p className="text-xs text-slate-600 mb-3">
                                Considering selling or letting? McGuinness Fleck offers discounted fees for block management clients.
                            </p>
                            <a href="tel:02890683020" className="text-blue-600 text-xs font-bold">Contact McGuinness Fleck</a>
                        </div>
                    </div>
                </div>
            );
        };

        const root = ReactDOM.createRoot(document.getElementById('root'));
        root.render(<App />);
    </script>
</body>
</html>
